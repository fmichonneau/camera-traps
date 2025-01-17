---
# Please do not edit this file directly; it is auto generated.
# Instead, please edit 02-OrganizingWhiskerbook.md in _episodes_rmd/
title: "Organizing Whiskerbook"
objectives: 
- "Create a xlsx file in the format suitable for upload to the Whikerbook interface"
questions: 
- "How can we properly organize the data for batch import into Whiskerbook?"
teaching: 60
exercises: 30
keypoints:
- "Whiskerbook takes a specific format for data upload"
- "Casting the file names into long format for Encounter.mediaAsset"
---



Set the working directory, and read in the camera trap data compiled in the previous lesson into this session.


```r
setwd("YourWorkingDirectory/WhiskerbookTrainingMaterials/")
```

```
## Error in setwd("YourWorkingDirectory/WhiskerbookTrainingMaterials/"): cannot change working directory
```

```r
SnowLeopardBook<-read.csv("SnowLeopard_CameraTrap.csv")
```

```
## Warning in file(file, "rt"): cannot open file 'SnowLeopard_CameraTrap.csv': No
## such file or directory
```

```
## Error in file(file, "rt"): cannot open the connection
```


Subset the data by columns that will be used for the Whiskerbook upload

```r
SnowLeopardBook<-SnowLeopardBook[,c(2,3,4,5,6,12,15,16,17,27,28)]
```

```
## Error in eval(expr, envir, enclos): object 'SnowLeopardBook' not found
```

Load the Whiskerbook template downloaded from the website to assist in preparing batch import files. This file contains the header column names that are necessary for the program to import the data and fields successfully. 


```r
Whiskerbook_template<-read.csv("WildbookStandardFormat.csv")
```

```
## Warning in file(file, "rt"): cannot open file 'WildbookStandardFormat.csv': No
## such file or directory
```

```
## Error in file(file, "rt"): cannot open the connection
```


First, we will expand the empty dataframe with NA values to populate the dataframe with as many rows as there are data from our camera trap records. 

```r
#first make sure there are enough rows in the template
Whiskerbook_template[1:nrow(SnowLeopardBook),]<-NA
```

```
## Error in Whiskerbook_template[1:nrow(SnowLeopardBook), ] <- NA: object 'Whiskerbook_template' not found
```

Now, with the template formatted correctly, we can simply add the data from the camera trap dataframe to the template. 


```r
#then add data from the camera trap dataframe to the template
Whiskerbook_template$Encounter.locationID<-SnowLeopardBook$Station
```

```
## Error in eval(expr, envir, enclos): object 'SnowLeopardBook' not found
```

```r
Whiskerbook_template$Encounter.mediaAssetX<-SnowLeopardBook$FileName
```

```
## Error in eval(expr, envir, enclos): object 'SnowLeopardBook' not found
```

```r
Whiskerbook_template$Encounter.decimalLatitude<-SnowLeopardBook$Lat
```

```
## Error in eval(expr, envir, enclos): object 'SnowLeopardBook' not found
```

```r
Whiskerbook_template$Encounter.decimalLongitude<-SnowLeopardBook$Long
```

```
## Error in eval(expr, envir, enclos): object 'SnowLeopardBook' not found
```

```r
Whiskerbook_template$Encounter.year<-SnowLeopardBook$Year
```

```
## Error in eval(expr, envir, enclos): object 'SnowLeopardBook' not found
```

```r
Whiskerbook_template$Encounter.genus<-SnowLeopardBook$Genus
```

```
## Error in eval(expr, envir, enclos): object 'SnowLeopardBook' not found
```

```r
Whiskerbook_template$Encounter.specificEpithet<-SnowLeopardBook$Species
```

```
## Error in eval(expr, envir, enclos): object 'SnowLeopardBook' not found
```

```r
Whiskerbook_template$Encounter.submitterID<-"Eve Bohnett"
```

```
## Error in Whiskerbook_template$Encounter.submitterID <- "Eve Bohnett": object 'Whiskerbook_template' not found
```

```r
Whiskerbook_template$Encounter.country<-"Afghanistan"
```

```
## Error in Whiskerbook_template$Encounter.country <- "Afghanistan": object 'Whiskerbook_template' not found
```

```r
Whiskerbook_template$Encounter.submitterOrganization<-"WCS Afghanistan"
```

```
## Error in Whiskerbook_template$Encounter.submitterOrganization <- "WCS Afghanistan": object 'Whiskerbook_template' not found
```

Since the dates in our camera trap dataset are not formatted properly, we will pull out the information for month and day from the date object and fill in new columns.


```r
#fix dates
SnowLeopardBook$Date<-as.Date(SnowLeopardBook$Date)
```

```
## Error in as.Date(SnowLeopardBook$Date): object 'SnowLeopardBook' not found
```

```r
Whiskerbook_template$Encounter.month<- format(SnowLeopardBook$Date, "%m") 
```

```
## Error in format(SnowLeopardBook$Date, "%m"): object 'SnowLeopardBook' not found
```

```r
Whiskerbook_template$Encounter.day<- format(SnowLeopardBook$Date, "%d")
```

```
## Error in format(SnowLeopardBook$Date, "%d"): object 'SnowLeopardBook' not found
```

```r
#we can simply use the substr function we learned about earlier to pull out the first two characters in the time string to get the hours only.
Whiskerbook_template$Encounter.hour<-substr(SnowLeopardBook$Time, 1,2) 
```

```
## Error in substr(SnowLeopardBook$Time, 1, 2): object 'SnowLeopardBook' not found
```

The Whiskerbook template requires that the data are put into a format with the image names of each occurrence in one row. An occurrence is a set of images that are normally associated to one time span, like an hour where the same animals were passing in front of the camera. An encounter is a sighting of one animal within that time span. It is possible to encounter more than one individual over the course of an hour, although that hour is still called an occurrence. 

For animal encounter data, it depends on the length of time you would like to subset the data. For the purposes of this lesson, we will group the data into occurences by hour. 

Here we use the dplyr functions for group_by to group the camera trap photos by location ID of the camera station, and the year, month, day, and hour. Then, after they are grouped, we simply sequentially number each individual photo and assign it to which group it is in. 

The mutate function within dplyr allows us to create a new row of data based on some function or command, in this case, we will use the cur_group_id() to ID each of the rows according to which group they are in. 


```
## Error: <text>:3:1: unexpected '}'
## 2: library(dplyr)
## 3: }
##    ^
```

```r
Whiskerbook_template<-Whiskerbook_template%>%
  group_by(Encounter.locationID, Encounter.year, Encounter.month, Encounter.day,Encounter.hour)%>%
  mutate(Encounter.occurrenceID = cur_group_id())
```

```
## Error in Whiskerbook_template %>% group_by(Encounter.locationID, Encounter.year, : could not find function "%>%"
```

Next, we have to sequentially number each of the images within that group to create the Encounter.mediaAsset information that Whiskerbook needs to have. Now, within each group we are numbering each photo within that group. There may be 10 photos in an occurrence so they would be numbered 1-10, or there may be 40 photos within the occurrence, so we name those 1-40.


```r
Whiskerbook_template<-Whiskerbook_template%>%
  group_by(Encounter.occurrenceID)%>%
  mutate(Encounter.mediaAsset = 1:n())
```

```
## Error in Whiskerbook_template %>% group_by(Encounter.occurrenceID) %>% : could not find function "%>%"
```

The numbers we just created are actually going to become column names, and so we need to add the characters "Encounter.mediaAsset" before these numbers. To do this, we can use the paste function to paste together our character string and the number we generated. 


```r
Whiskerbook_template$Encounter.mediaAsset<-paste("Encounter.mediaAsset", Whiskerbook_template$Encounter.mediaAsset, sep="_")
```

```
## Error in paste("Encounter.mediaAsset", Whiskerbook_template$Encounter.mediaAsset, : object 'Whiskerbook_template' not found
```

Now, we will cast the Encounter.mediaAsset column out. Which means we will take one column of data, and generate numerous columns. Check to see the result of this if you are unsure what just happened.

We are calling this new template Whiskerbook_template2


```r
library(reshape2)
Whiskerbook_template2<-dcast(Whiskerbook_template,Encounter.occurrenceID~Encounter.mediaAsset, value.var ="Encounter.mediaAssetX")
```

```
## Error in value.var %in% names(data): object 'Whiskerbook_template' not found
```


As you can see, the columns are not sorted sequentially, so we need to sort them by ascending order. The str_sort function in the stringr can sort the columns.


```r
library(stringr)
Whiskerbook_template2_cols<-str_sort(colnames(Whiskerbook_template2), numeric = TRUE)
```

```
## Error in is.data.frame(x): object 'Whiskerbook_template2' not found
```

```r
Whiskerbook_template2<-Whiskerbook_template2[,Whiskerbook_template2_cols]
```

```
## Error in eval(expr, envir, enclos): object 'Whiskerbook_template2' not found
```

Next, we have to actually rename all of the Encounter.mediaAsset columns starting with 0. They start with 1 now because  the dply package requires we number starting with 1 not 0. It's a bit of a glitch for us, but we can fix this. 

First, we can create a vector of numbers for the number of occurrences that we have. Then, we add the characters "Encounter.mediaAsset" to these numbers. Then we add one more column name for the "Encounter.occurrenceID" that is already in our dataframe. Now we have a vector of character strings that will be our new column names. Now, we can simply rename our dataframe columns.


```r
#the columns have to be renamed from 0 so we subtract one from the length
#the final column is the Encounter.occurrenceID column so we subtract one
col_vec<-0:(length(Whiskerbook_template2)-2)
```

```
## Error in eval(expr, envir, enclos): object 'Whiskerbook_template2' not found
```

```r
col_vec<-paste("Encounter.mediaAsset",col_vec, sep="")
```

```
## Error in paste("Encounter.mediaAsset", col_vec, sep = ""): object 'col_vec' not found
```

```r
Media_assets<-c(col_vec, "Encounter.occurrenceID")
```

```
## Error in eval(expr, envir, enclos): object 'col_vec' not found
```

```r
names(Whiskerbook_template2)<-Media_assets
```

```
## Error in eval(expr, envir, enclos): object 'Media_assets' not found
```


The next thing we need to do is clean up our original Whiskerbook template so that we can import this data. Right now, we have all the filenames in an Encounter.mediaAssetX column, and we need to remove that. 

Then, we will take only the unique records within this template, and remove all of the columns which are filled with only NA values. We do not need these columns if they have no information and the file can still be uploaded. 

Finally, we can remove the Encounter.mediaAsset column, which contained the numbers assigned to the photos. 


```r
Whiskerbook_template<-Whiskerbook_template[,-1]
```

```
## Error in eval(expr, envir, enclos): object 'Whiskerbook_template' not found
```

```r
Whiskerbook_template<-unique(Whiskerbook_template)
```

```
## Error in unique(Whiskerbook_template): object 'Whiskerbook_template' not found
```

```r
Whiskerbook_template<-Whiskerbook_template[,colSums(is.na(Whiskerbook_template))<nrow(Whiskerbook_template)]
```

```
## Error in eval(expr, envir, enclos): object 'Whiskerbook_template' not found
```

```r
Whiskerbook_template <-Whiskerbook_template[,-ncol(Whiskerbook_template)]
```

```
## Error in eval(expr, envir, enclos): object 'Whiskerbook_template' not found
```

Now our original Whiskerbook template is formatted so we can merge the Whiskerbook_template2 with our filenames to it. To do this, we can merge the templates together using the merge function, and then select only the unique rows.


```r
Whiskerbook<-merge(Whiskerbook_template,Whiskerbook_template2, by="Encounter.occurrenceID", all.x=FALSE, all.y=TRUE)
```

```
## Error in merge(Whiskerbook_template, Whiskerbook_template2, by = "Encounter.occurrenceID", : object 'Whiskerbook_template' not found
```

```r
Whiskerbook<-unique(Whiskerbook)
```

```
## Error in unique(Whiskerbook): object 'Whiskerbook' not found
```

Finally, we are left with a template with our occurrences with the filenames cast into rows. We will write this to file for batch import into Whiskerbook. 


```r
write.csv(Whiskerbook, "Whiskerbook.csv")
```

```
## Error in is.data.frame(x): object 'Whiskerbook' not found
```





> ## Challenge: Whiskerbook 

> 
> Answer the following questions:
> 
> 1. What are some of the original fields in this Whiskerbook template that may be useful that we did not use?
> > 
> {: .solution}
{: .challenge}

{% include links.md %}
