---
title: "Introduction to Data Carpentry for Camera Traps"
teaching: 10
exercises: 0
questions:
- "How can we use program R and Wildbook to organize our data for Spatial Capture Recapture"
objectives:
- "Organize camera trap raw data into a format to upload to the Wildbook platform"
- "Understand the functionality of Wildbook"
- "Manipulate the data that is exported from Wildbook"
- "Prepare data in a format to run in spatial capture recapture modeling using the oSCR package"
- "Run an oSCR model"

keypoints:
- "Use camtrapR package in program R to organize camera trap data"
- "Create a data upload format compatible for Wildbook"
- "Use Wildbook for sorting camera trap data by individual identity"
- "Format the data frame that includes encounters of individual identities into a data format for spatial capture recapture"
- "Create buffer masks for the spatial recapture format"
- "run models using the oSCR package in program R"


---
Camera traps provide a non-invasive survey method for detecting animals. Downloading and analyzing camera trap data can be faster and more organized with knowledge of R programming to preprocess data prior to species or individual identification tasks, understanding software options and how to use them, and postprocess spreadsheet datasets in preparation for modeling.

This is an introduction to Camera Trap data organization and wranging designed for participants with no programming experience. These lessons can be taught in one and a half days (~ 10 hours). They start with some basic information about R syntax, and move through how to import camera trap files, using the camtrapR package to work with camera trap exif data and combine with field survey data frames, an introduction to Megadetector and Wildlife Insights for species level identification, how to import and use the Wildbook platform for individual identification, how to use dplyr package calculate summary information from a data frame, organizing dataframes for occupancy or spatial capture recapture data analysis. A final section will include an exmple of running models using the oSCR package. 

> ## Getting Started
>
> Data Carpentry's teaching is hands-on, so participants are encouraged to use
> their own computers to ensure the proper setup of tools for an efficient
> workflow. <br>**These lessons assume some knowledge and the skills or tools.**
>
> To get started, follow the directions in the "[Setup][lesson-setup]" tab to
> download data to your computer and follow any installation instructions.
>
> #### Prerequisites
>
> This lesson requires a working copy of **R**.
> <br>To most effectively use these materials, please make sure to install
> everything *before* working through this lesson.
> 
> ### R Skill Level
> This lesson assumes you have some knowledge of `R`. If you've never 
> used `R` before, or need a refresher, start with our
> [Introduction to R for Geospatial Data](http://www.datacarpentry.org/r-intro-geospatial/)
> lesson.
>
  > ### Geospatial Skill Level
  > This lesson assumes you have basic knowledge of geospatial data types
> and common file formats. If you have never worked with geospatial
> data before, or need a refresher, start with our
> [Introduction to Geospatial Concepts](http://www.datacarpentry.org/organization-geospatial/)
> lesson.
>
{: .prereq}

> ## For Instructors
> If you are teaching this lesson in a workshop, please see the
> [Instructor notes](guide).
{: .prereq}

{% include links.md %}


