---
# Please do not edit this file directly; it is auto generated.
# Instead, please edit 02-OrganizingCameraTraps.md in _episodes_rmd/
title: "Organizing Camera Trap Data"
objectives:
- "Perform camera trap organizational steps like renaming files"
- "Extract exif data from camera traps"
- "Combine dataframes with locational information"
questions: 
- "How can we use program R and package camTrapR to organize camera trap data?"
teaching: 60
exercises: 30
keypoints:
- "Load camera trap data into R with the camtrapR package"
- "Rename photos according to trap location and date, then copy to a new folder"
- "When character strings between two dataframes do not match the str_replace() function can replace or change parts of the strings for a column in a dataframe"
- "Spatial objects can be projected using the st_transform() function"
---
  


In camera trapping studies, it's common to have a lot of camera photos, sometimes thousands or even millions of images that need to be processed and formatted for analysis tasks. Camera trap data organization requires careful management and data extraction that can be greatly assisted by the use of programming tools, like program R and Python. 

In general, if we want to organize our data from raw camera trapping data, there will also be other files including GPS locations, and camera start-times and end times. 

First, set the working directory for the workshop where the snow leopard data have been downloaded.


```r
#Set working directory
setwd("C:/Users/evebo/OneDrive/Desktop/WhiskerbookTrainingMaterials/")
```

```
## Error in setwd("C:/Users/evebo/OneDrive/Desktop/WhiskerbookTrainingMaterials/"): cannot change working directory
```

```r
#Make sure to have ExifTool installed and bring in the camtrapR package
library(camtrapR)
```

Set the file path of your image directory

```r
# raw image location
wd_images_raw <- file.path("2012_CTdata")   
```

One of the first steps that we want to perform is to perform a data quality check. Make sure that each of the file folders has the name of the camera trap station, often including the name and number. In our case we have cameras with names like "C1_Avgarch_SL", which indicates that this camera station was numbered 1, and at a location named Avgarch. These names are consistent across data tables with the GPS coordinates and camera performance information as well making it easier to merge this information. 

Since SD cards often name files sequentially like "IMG_1245.jpg", then there may be more than one file with this name. Our goal is to give each image a unique filename that uses the location, date/time, and a sequential number, so that the photo filenames are unique. To do this, the folder names have to have the location information.

To create a new directory for our copied data we can use the dir.create function and set the file.path for a new directory renamed images to be copied to.


```r
#create directory
dir.create("2012CameraData_renamed")
```

```
## Warning in dir.create("2012CameraData_renamed"): '2012CameraData_renamed'
## already exists
```

```r
#get the file path for the new directory and store in an object
wd_images_raw_renamed <- file.path("2012CameraData_renamed")  
```


Some camera trap models (like Reconyx) do not use the standard Exif metadata information for the date and time, which makes it not possible to read directly, so we use the fixDateTimeOriginal function in the camTrapR package. 


```r
#fix date time objects that may not be in standard Exif format
fixDateTimeOriginal(wd_images_raw,recursive = TRUE)
```

```
## Error: cannot find ExifTool
```

Renaming camera trap files is possible using the imageRename function. Here we specify the input and output directories.

There are additional parameters for whether the directories contain multiple cameras at the station, like an A and B station opposing each other (hasCameraFolders). In our case, our folders do have subdirectories, but they are not specific to a substation, so we will set this to false. 

Additional parameters include whether the camera subdirectories should be kept (keepCameraSubfolders), and we do not have extra station or species subdirectories we can also keep this as FALSE. We will set copyImages to TRUE because we want these images to go into a new directory. 


```r
#rename images
renaming.table2 <- imageRename(inDir               = wd_images_raw,
                               outDir              = wd_images_raw_renamed,   
                               hasCameraFolders    = FALSE,
                               keepCameraSubfolders = FALSE,
                               copyImages          = TRUE)
```

```
## Error: Could not find inDir:
## 2012_CTdata
```

Next, we will create a record table or dataframe of the exif information, that includes station, species, date/time, and directory information.

There are parameters to allow a record table to include available species information to be sorted within the directory, for example (Station/Species) or (Station/Camera/Species). In our case, we only have one species, snow leopard images, so we will not use these extra settings. 


```r
#create a dataframe from exif data that includes date and time
rec.db.species0 <- recordTable(inDir  = wd_images_raw_renamed,
                               IDfrom = "directory")
```

```
## Error: cannot find ExifTool
```

After inspecting the dataframe, we can see there is a Species column with the wrong information in it, so let's fix that

```r
#change the species column contents
rec.db.species0$Species <- "uncia"
```

```
## Error in rec.db.species0$Species <- "uncia": object 'rec.db.species0' not found
```

```r
rec.db.species0$Genus <- "Pathera"
```

```
## Error in rec.db.species0$Genus <- "Pathera": object 'rec.db.species0' not found
```

To save this table to a csv file we can write this to file, so we have the raw exif data if we need it. 


```r
#write the Exif data to file
write.csv(rec.db.species0, "CameraTrapExifData.csv")
```

```
## Error in is.data.frame(x): object 'rec.db.species0' not found
```


Now we have the exif data finished and in a dataframe format. Next we are going to bring in the data from the GPS coordinates. By loading the dataframe into the program.


```r
#load the camera trap GPS and camera function information
WakhanData<-read.csv("Metadata_CT_2012.csv")
```

```
## Warning in file(file, "rt"): cannot open file 'Metadata_CT_2012.csv': No such
## file or directory
```

```
## Error in file(file, "rt"): cannot open the connection
```

```r
#look at the synatx of the geometry column for GPS coordinates
WakhanData$Loc_geo[1]
```

```
## Error in eval(expr, envir, enclos): object 'WakhanData' not found
```

When we inspect these data, two empty rows have no information, so we'll have to clean this up a bit. There are several ways of doing this, for one, we can use the complete.cases function. 



```r
#remove the two rows with missing data
WakhanData<-WakhanData[complete.cases(WakhanData),]
```

```
## Error in eval(expr, envir, enclos): object 'WakhanData' not found
```


Another factor can see that the location column with the coordinates has a format with the coordinates in one string. So, to fix this we need to do a bit of work to get it into the format that we want. To do this we can use the substr function to get a substring of the data out of the string. Since they are all the same format we can simply pull out the numbers that we want using the place of the characters in the string. For example, to get the lattitude coordinates, we need to pull out the 5th to 11th characters in the string.


```r
# double check the substring of the UTM coordinates to extract
substr(WakhanData$Loc_geo, 5,11)
```

```
## Error in substr(WakhanData$Loc_geo, 5, 11): object 'WakhanData' not found
```

We can assign these new strings to new columns in our dataframe.

```r
#add the Easting and Northing data to new X and Y columns
WakhanData$X<-substr(WakhanData$Loc_geo, 5,11)
```

```
## Error in substr(WakhanData$Loc_geo, 5, 11): object 'WakhanData' not found
```

```r
WakhanData$Y<-substr(WakhanData$Loc_geo, 13,nchar(WakhanData$Loc_geo[1]))
```

```
## Error in substr(WakhanData$Loc_geo, 13, nchar(WakhanData$Loc_geo[1])): object 'WakhanData' not found
```

Great so we have our Latitude and Longitude coordinates. Let's now merge the dataframe with the exif data and the dataframe with the GPS coordinates and camera infromation together. Before we can do that, we need to make sure that there is a column in both that match completely. So let's have a check and see if the trap names in the record table are the same in the GPS coordinates table. 


```r
#add the check the camera trap station names between the two dataframes
unique(rec.db.species0$Station)
```

```
## Error in unique(rec.db.species0$Station): object 'rec.db.species0' not found
```

```r
unique(WakhanData$Trap.site)
```

```
## Error in unique(WakhanData$Trap.site): object 'WakhanData' not found
```

From this result we can see nearly all of the camera traps are different because there is an extra _SL at the end of the names, so we can remove it. We can use the stringr package and function str_remove to apply a removal.


```r
#remove characters "_SL" from the record table station names
library(stringr)
rec.db.species0$Station<-str_remove(rec.db.species0$Station, "_SL")
```

```
## Error in stri_replace_first_regex(string, pattern, fix_replacement(replacement), : object 'rec.db.species0' not found
```


We can use the setdiff function to determine if any of the trap names are still different. Oftentimes, there are misspellings. 


```r
#check if the site names are the same
setdiff(unique(rec.db.species0$Station), unique(WakhanData$Trap.site))
```

```
## Error in unique(rec.db.species0$Station): object 'rec.db.species0' not found
```

We find two cameras have different names. To fix this, we can use the str_replace function in the stringr package


```r
#replace bad station names with correct spellings
WakhanData$Trap.site<-str_replace(WakhanData$Trap.site,"C5_Ishmorg" , "C5_Ishmorgh")
```

```
## Error in stri_replace_first_regex(string, pattern, fix_replacement(replacement), : object 'WakhanData' not found
```

```r
WakhanData$Trap.site<-str_replace(WakhanData$Trap.site,"C18_Khandud" , "C18_Khundud")
```

```
## Error in stri_replace_first_regex(string, pattern, fix_replacement(replacement), : object 'WakhanData' not found
```

```r
setdiff(unique(rec.db.species0$Station), unique(WakhanData$Trap.site))
```

```
## Error in unique(rec.db.species0$Station): object 'rec.db.species0' not found
```

Now, there is one more problem with our dataset, and that is that our coordinates are only in UTM coordinate system, and we actually need them in a Lat/Long coordinate system to upload them to the Whiskerbook format. 

To work with the spatial formats and convert these coordinates, we will use the sf package. We can first create a few objects of the coordinate systems that we will be using. 


```r
#load sf package and set coordinate systems to objects
library(sf)
wgs84_crs = "+init=EPSG:4326"
UTM_crs = "+init=EPSG:32643"
```



```r
#convert the GPS coordinates into shapefile points
WakhanData_points<-st_as_sf(WakhanData, coords=c("X","Y"), crs=UTM_crs)
```

```
## Error in st_as_sf(WakhanData, coords = c("X", "Y"), crs = UTM_crs): object 'WakhanData' not found
```

```r
plot(WakhanData_points[,"Year"])
```

```
## Error in plot(WakhanData_points[, "Year"]): object 'WakhanData_points' not found
```

Here we will convert the coordinate system to WGS84.


```r
#convert the coordinate system from UTM to lat long WGS84
WakhanData_points_latlong<-st_transform(WakhanData_points, crs=wgs84_crs)
```

```
## Error in st_transform(WakhanData_points, crs = wgs84_crs): object 'WakhanData_points' not found
```

```r
WakhanData_points_latlong_df<- st_coordinates(WakhanData_points_latlong)
```

```
## Error in st_coordinates(WakhanData_points_latlong): object 'WakhanData_points_latlong' not found
```

```r
colnames(WakhanData_points_latlong_df)<-c("Lat","Long")
```

```
## Error in colnames(WakhanData_points_latlong_df) <- c("Lat", "Long"): object 'WakhanData_points_latlong_df' not found
```

```r
WakhanData <-cbind(WakhanData, WakhanData_points_latlong_df)
```

```
## Error in cbind(WakhanData, WakhanData_points_latlong_df): object 'WakhanData' not found
```


Now we can merge the two dataframes together using the merge function. We can set the columns we want to match on using the by.x and by.y arguments, and then set the all=FALSE because some of the records in the datatable with the GPS coordinates we do not have camera data for, so we do not need them in the final dataframe. The all argument can be set to true to include all records in both tables, but in this case we only want to merge the data from the first table that matches the second table. 



```r
#merge the record table to the GPS coordinates
final_CameraRecords<-merge(rec.db.species0, WakhanData, by.x="Station", by.y="Trap.site", all=TRUE)
```

```
## Error in merge(rec.db.species0, WakhanData, by.x = "Station", by.y = "Trap.site", : object 'rec.db.species0' not found
```

Now let's save this file for later. 


```r
#write the file to csv
write.csv(final_CameraRecords, "SnowLeopard_CameraTrap.csv")
```

```
## Error in is.data.frame(x): object 'final_CameraRecords' not found
```



> ## Challenge: Renaming Files and changing CRS

> 
> Answer the following questions:
> 
> 1. What would we do for renaming our files if we had different camera stations?
> 
> 2. What if our data were in the Namdapha National Park? What CRS would we use, and how would we code this in R?
> 
> > 
> > ## Answers
> > 
> > The imageRename function in the camtrapR function allows for subdirectory folders to be organized separately. See the help for imageRename function ?imageRename to find out the station directories can have subdirectories "inDir/StationA/Camera1" to organize two cameras per station. 

> >
> > The  Namdapha National Park is in Northeastern India, and is WGS 1984 UTM Zone 4N
>> the EPSG code is 32604 "+init=EPSG:32643"
> {: .solution}
{: .challenge}

{% include links.md %}
