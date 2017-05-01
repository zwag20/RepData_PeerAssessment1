---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data
* Load the data (i.e. read.csv())
* Process/transform the data (if necessary) into a format suitable for your analysis



```r
activity <- read.csv("activity.csv")

df_Total <- aggregate(steps ~ date, data = activity, sum, na.rm = TRUE)
```
## What is mean total number of steps taken per day?
* Calculate the total number of steps taken per day
* If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day


```r
hist(df_Total$steps)
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png)
#Calculate and report the mean and median total number of steps taken per day

```r
mean(df_Total$steps)
```

```
## [1] 10766.19
```

```r
median(df_Total$steps)
```

```
## [1] 10765
```
## What is the average daily activity pattern?
* Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
df_interval <- aggregate(steps~interval, data = activity, mean, na.rm=TRUE)
plot(steps~interval,data=df_interval,type="l")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png)

* Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
df_interval[which.max(df_interval$steps),]
```

```
##     interval    steps
## 104      835 206.1698
```
## Imputing missing values

* Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```

* Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.


```r
df_complete <- merge(activity,df_interval, by="interval")
df_complete$steps.x[is.na(df_complete$steps.x)] <- df_complete$steps.y[is.na(df_complete$steps.x)]
```

* Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
df_complete <- df_complete[c(1:3)]
names(df_complete)[2] <- "steps"
```

* Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
df_Total2 <- aggregate(steps~date,data=df_complete,sum)
hist(df_Total2$steps)
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png)

```r
mean(df_Total2$steps)
```

```
## [1] 10766.19
```

```r
median(df_Total2$steps)
```

```
## [1] 10766.19
```
The mean value is the same and the median is slightly higher.

## Are there differences in activity patterns between weekdays and weekends?

* Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.
 
 ```r
 df_complete$day <- weekdays(as.Date(df_complete$date))
 df_complete$week <- "weekday"
 df_complete$week[df_complete$day %in% c("Saturday","Sunday")] <- "weekend"
 ```

* Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
library(ggplot2)
df_interval2 <- aggregate(steps~interval+week,df_complete,mean)
ggplot(data=df_interval2, aes(x=interval, y=steps, group=week)) + geom_line(aes(color=week))+ facet_wrap(~ week, nrow=2)
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png)
