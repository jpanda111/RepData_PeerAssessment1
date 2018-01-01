---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---
  

```r
knitr::opts_chunk$set(echo = TRUE, results = "asis")
```

## reading in the dataset and processing the data

```r
data <- read.csv("activity.csv", header = TRUE, sep = ",")
```

## histogram of the total number of steps taken each day

```r
totalsteps<- with(data,tapply(steps,date,sum,na.rm=T))
hist(totalsteps)
```

![](PA1_template_files/figure-html/histoftotalsteps-1.png)<!-- -->

```r
##hist(totalsteps,breaks=30,xlab=",,,",col="yellow")
```

## Mean and median number of the total steps taken each day

```r
mean(totalsteps)
```

[1] 9354.23

```r
median(totalsteps)
```

[1] 10395

## Time series plot of the average number of steps taken

```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
avesteps <- data %>% group_by(interval) %>% summarize(avesteps=mean(steps,na.rm=TRUE))
plot(avesteps,type="l",xlab="5 mins Per Interval", ylab="Average Steps", main ="Average Steps Per Interval")
```

![](PA1_template_files/figure-html/unnamed-chunk-1-1.png)<!-- -->

## The 5-minute interval that, on average, contains the maximum number of steps

```r
avesteps$interval[which.max(avesteps$avesteps)]
```

[1] 835

## one strategy for imputing missing data
#####All of the missing values are filled in with mean value for each specific 5-minute interval

```r
sum((rowSums(is.na(data)))>=1)
```

[1] 2304

```r
newdata<-merge(data,avesteps,by="interval",all=TRUE)
index<-is.na(newdata$steps)
newdata$steps[index]<-newdata$avesteps[index]
newdata<-newdata[order(newdata$date),1:3]
```

## Histogram of the total number of steps taken each day after missing values are imputed
#####the mean and median values are higher after imputing missing data. That's because before imputing, NA data are removed.

```r
totalstepsnew<- with(newdata,tapply(steps,date,sum,na.rm=T))
hist(totalstepsnew)
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

```r
mean(totalstepsnew)
```

[1] 10766.19

```r
median(totalstepsnew)
```

[1] 10766.19

## Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends

```r
library(lattice)
Sys.setlocale("LC_TIME","C")
```

[1] "C"

```r
newdata$date<-as.Date(newdata$date)
weekend<-weekdays(newdata$date) %in% c("Saturday","Sunday")
newdata$weeks<-factor(weekend,labels=c("weekday","weekend"))
aveactivitydata<-aggregate(steps~interval+weeks,data=newdata,FUN="mean")
with(aveactivitydata,xyplot(steps~interval|weeks,layout=c(1,2),type="l",ylab="Number of steps",xlab="Interval"))
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

## All the R code needed to reproduce the results in the report are included above

```r
##library(knit)
##knit2html(spin("PA1_template.Rmd",knit=FALSE),force_v1 = TRUE)
```

