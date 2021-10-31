---
# Please do not edit this file directly; it is auto generated.
# Instead, please edit 07-SpatialCaptureRecapture_Modeling.md in _episodes_rmd/
title: "Spatial-Capture Recapture Modeling"
objectives:
- "Perform single session spatial capture recapture modeling tasks"
- "Read outputs for density, abundance, detectability and sigma"

questions: 
- "How to setup and run oSCR models?"
- "How to interpret the model outputs?"

teaching: 60
exercises: 30
source: Rmd
---



~~~
## Warning in file(filename, "r", encoding = encoding): cannot open file '../
## setup.R': No such file or directory
~~~
{: .warning}



~~~
## Error in file(filename, "r", encoding = encoding): cannot open the connection
~~~
{: .error}

Use the oSCR.fit function with no covariates, use the scrFrame and alltraps_df that we generated earlier.

Then use predict.oSCR onto the same data to get our predictions. 

Note that this will take around 5 minutes to run.


~~~
snowLeopard.1<- oSCR.fit(list(D ~ 1, p0 ~ 1, sig ~ 1), scrFrame, list(alltraps_df))
~~~
{: .language-r}



~~~
Error in oSCR.fit(list(D ~ 1, p0 ~ 1, sig ~ 1), scrFrame, list(alltraps_df)): could not find function "oSCR.fit"
~~~
{: .error}



~~~
pred<-predict.oSCR(snowLeopard.1, scrFrame,list(alltraps_df), override.trim =TRUE )
~~~
{: .language-r}



~~~
Error in predict.oSCR(snowLeopard.1, scrFrame, list(alltraps_df), override.trim = TRUE): could not find function "predict.oSCR"
~~~
{: .error}


We can plot the estimates for density across the study area to see how it looks

~~~
library(viridis)
~~~
{: .language-r}



~~~
Loading required package: viridisLite
~~~
{: .output}



~~~
myCol = viridis(7)
RasterValues_1<-as.matrix(pred$r[[1]])
~~~
{: .language-r}



~~~
Error in as.matrix(pred$r[[1]]): object 'pred' not found
~~~
{: .error}



~~~
MaxRaS<-max(RasterValues_1, na.rm=TRUE)
~~~
{: .language-r}



~~~
Error in eval(expr, envir, enclos): object 'RasterValues_1' not found
~~~
{: .error}



~~~
MinRaS<-min(RasterValues_1,na.rm=TRUE)
~~~
{: .language-r}



~~~
Error in eval(expr, envir, enclos): object 'RasterValues_1' not found
~~~
{: .error}



~~~
plot(pred$r[[1]], col=myCol,
     main="Realized density",
     xlab = "UTM Westing Coordinate (m)", 
     ylab = "UTM Northing Coordinate (m)")
~~~
{: .language-r}



~~~
Error in plot(pred$r[[1]], col = myCol, main = "Realized density", xlab = "UTM Westing Coordinate (m)", : object 'pred' not found
~~~
{: .error}



~~~
points(tdf2[,3:4], pch=20)
~~~
{: .language-r}



~~~
Error in points(tdf2[, 3:4], pch = 20): object 'tdf2' not found
~~~
{: .error}


Backtransforming the estimates to be in the 100km2 units for density that we want using ht emu

~~~
pred.df.dens <- data.frame(Session = factor(1))
#make predictions on the real scale
(pred.dens <- get.real(snowLeopard.1, type = "dens", newdata = pred.df.dens, d.factor = multiplicationfactor))
~~~
{: .language-r}



~~~
Error in get.real(snowLeopard.1, type = "dens", newdata = pred.df.dens, : could not find function "get.real"
~~~
{: .error}

Get the abundance, detection, and sigma parameters

~~~
(total.abundance <- get.real(snowLeopard.1, type = "dens", newdata = pred.df.dens, d.factor=nrow(snowLeopard.1$ssDF[[1]])))
~~~
{: .language-r}



~~~
Error in get.real(snowLeopard.1, type = "dens", newdata = pred.df.dens, : could not find function "get.real"
~~~
{: .error}


~~~
(pred.det <- get.real(snowLeopard.1, type = "det", newdata = pred.df.dens))
~~~
{: .language-r}



~~~
Error in get.real(snowLeopard.1, type = "det", newdata = pred.df.dens): could not find function "get.real"
~~~
{: .error}


~~~
(pred.sig <- get.real(snowLeopard.1, type = "sig", newdata = pred.df.dens))
~~~
{: .language-r}



~~~
Error in get.real(snowLeopard.1, type = "sig", newdata = pred.df.dens): could not find function "get.real"
~~~
{: .error}