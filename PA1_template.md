---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data
1. Load the data by unzipping the archive and reading the data from csv. This can be done by the following command:

```r
unzip("activity.zip")
data<-read.csv("activity.csv")
```


## What is mean total number of steps taken per day?
1. Total number of steps taken per day are calcualted using the aggregate function. 

```r
stepsbydate <- aggregate(steps ~ date, data = data, FUN = sum)
```
2. Histogram of the total number of steps taken per day is generated using the following code:

```r
barplot(stepsbydate$steps, names.arg = stepsbydate$date, xlab = "date", ylab = "steps")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

3. The mean and median of total number of steps taken per day.

```r
mean(stepsbydate$steps)
```

```
## [1] 10766.19
```

```r
median(stepsbydate$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

1. Time series plot of 5-minute interval and the average number of steps taken (x-axis), averaged accross all days (y-axis):

```r
intervalsteps<-aggregate(steps~interval,data=data,FUN=mean)
plot(intervalsteps,type="l")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 
2. The 5-minute interval which contains the maximum number of steps

```r
intervalsteps$interval[which.max(intervalsteps$steps)]
```

```
## [1] 835
```

## Imputing missing values
1. The total number of missing values in the dataset

```r
sum(is.na(data))
```

```
## [1] 2304
```
2. Create a new dataset by filling the NA values with the corresponding mean of the 5-minute interval

```r
newdata <- merge(data, intervalsteps, by = "interval", suffixes = c("", ".y"))
nas <- is.na(newdata$steps)
newdata$steps[nas] <- newdata$steps.y[nas]
newdata <- newdata[, c(1:3)]
```
3. Histogram of the totatl number of steps taken each day after missing data has been filled:

```r
newstepsbydate <- aggregate(steps ~ date, data = newdata, FUN = sum)
barplot(newstepsbydate$steps, names.arg = newstepsbydate$date, xlab = "date", ylab = "steps")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png) 
4. The mean and median of the modified dataset

```r
mean(newstepsbydate$steps)
```

```
## [1] 10766.19
```

```r
median(newstepsbydate$steps)
```

```
## [1] 10766.19
```

As can be seen, there is no difference in the mean of the population. However there is a very slight difference in the median.

## Are there differences in activity patterns between weekdays and weekends?
1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day

```r
daytype <- function(date) {
    if (weekdays(as.Date(date)) %in% c("Saturday", "Sunday")) {
        "weekend"
    } else {
        "weekday"
    }
}
newdata$daytype <- as.factor(sapply(newdata$date, daytype))
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```r
par(mfrow = c(2, 1))
for (type in c("weekend", "weekday")) {
    stepsbytype <- aggregate(steps ~ interval, data = newdata, subset = newdata$daytype == 
        type, FUN = mean)
    plot(stepsbytype, type = "l", main = type)
}
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png) 
