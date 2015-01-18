# Reproducible Research: Peer Assessment 1



## Loading and preprocessing the data

```r
mydat <- read.csv("activity.csv")
# cleanup date
mydat$date <- as.Date(as.character(mydat$date))

#clean NA's...set to zero
newdat <-mydat[!is.na(mydat$steps),]

#use plyr to get sum, mean and median number of steps each day
library(plyr)
datsum<-ddply(newdat,"date",summarize,totsteps=sum(steps))
datmean<-ddply(newdat,"date",summarize,meansteps=mean(steps))
datmedian<-ddply(newdat,"date",summarize,medsteps=median(steps))
```


## Histogram of the total number of steps taken each day

```r
  hist(datsum$totsteps)
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

```r
# calc mean and median numer of total steps per day
meansteps <-mean(datsum$totsteps)
mediansteps <- median(datsum$totsteps)
```

## What is the mean total number of steps taken per day?
The mean total number of steps is 1.0766189\times 10^{4}

The median total number of steps is 10765


## What is the average daily activity pattern?



## Imputing missing values

```r
# calculate number of missing samples
misssamp <- sum(is.na(mydat$steps))
```
There are  2304 missing samples

## Are there differences in activity patterns between weekdays and weekends?
