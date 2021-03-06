# Reproducible Research: Peer Assessment 1



## Loading and preprocessing the data

```r
# read in activity.csv
mydat <- read.csv("activity.csv")
# cleanup date
mydat$date <- as.Date(as.character(mydat$date))

#clean NA's...remove for now
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
The mean total number of steps is **1.0766189\times 10^{4}**

The median total number of steps is **10765**


## What is the average daily activity pattern?

```r
# calculate mean #steps for each 5-minute interval

minuteData <- ddply(newdat,"interval",summarize,minavg=mean(steps))
plot(minuteData$interval,minuteData$minavg,type='l',xlab='interval',ylab='Average steps')
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

```r
# determine which interval contains the maximum number of steps
maxInterval <- minuteData$interval[which(minuteData$minavg == max(minuteData$minavg))]
```

The interval with the maximum number of steps is **835**

-------

## Imputing missing values

```r
# calculate number of missing samples
misssamp <- sum(is.na(mydat$steps))

# determine which rows have missing data
missRows <- which(is.na(mydat$steps))
#loop over the missing rows and copy in the appropriate 5-minute average
#create new data set first
newDataSet <- mydat

for (i in missRows) {

  newDataSet$steps[i] = minuteData$minavg[minuteData$interval==newDataSet$interval[i]]
}
# calculate sum on updated dataset
newDataSum<-ddply(newDataSet,"date",summarize,totsteps=sum(steps))

  #hist(newDataSum$totsteps)
# calc mean and median numer of total steps per day
newmeansteps <-mean(newDataSum$totsteps)
newmediansteps <- median(newDataSum$totsteps)
```
There are  **2304** missing samples.

Using the 5-minute average for the same window each day, the NA values were replaced. 

The updated mean number of steps is **1.0766189\times 10^{4}**

The updated median number of steps is **1.0766189\times 10^{4}**


```r
# calculate differences in the mean/median steps for the two datasets

deltamean= newmeansteps - meansteps
deltamedian = newmediansteps - mediansteps
```

This shows a delta of **0** steps in the mean and **1.1886792** steps in the median


### Histogram on updated data set

```r
hist(newDataSum$totsteps)
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png) 




## Are there differences in activity patterns between weekdays and weekends?


```r
# create weekend/weekday factor
# there is probably a better way to do this, but at least this works
newDataSet$dow <- weekdays(newDataSet$date)
newDataSet$dow[newDataSet$dow=="Sunday" | newDataSet$dow == "Saturday"]  <- 'weekend'
newDataSet$dow[newDataSet$dow != 'weekend'] <- 'weekday'
newDataSet$dowFact <- as.factor(newDataSet$dow)

weekendSubset=newDataSet[newDataSet$dowFact=="weekend",]
weekdaySubset=newDataSet[newDataSet$dowFact=="weekday",]
#str(newDataSet)
weekdayMinuteData <- ddply(weekdaySubset,"interval",summarize,minavg=mean(steps))
weekendMinuteData <- ddply(weekendSubset,"interval",summarize,minavg=mean(steps))

#head(weekdayMinuteData)
par(mfrow=c(2,1))
plot(weekdayMinuteData$interval,weekdayMinuteData$minavg,type='l',xlab='interval',ylab='Average steps',main="Weekday")

plot(weekendMinuteData$interval,weekendMinuteData$minavg,type='l',xlab='interval',ylab='Average steps', main="Weekend")
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png) 

