# Reproducible Research: Peer Assessment 1

## Loading and preprocessing the data

### Load the data (i.e. read.csv())


```r
activity <- read.csv("activity/activity.csv")
head(activity)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```

### Process/transform the data (if necessary) into a format suitable for your analysis


```r
class(activity$steps)
```

```
## [1] "integer"
```

```r
class(activity$date)
```

```
## [1] "factor"
```

```r
class(activity$interval)
```

```
## [1] "integer"
```

```r
activity$date <- as.Date(activity$date, "%Y-%m-%d")

class(activity$date)
```

```
## [1] "Date"
```

## What is mean total number of steps taken per day?

### Calculate the total number of steps taken per day


```r
total_steps_by_date <- aggregate(steps ~ date, activity, sum)

# create histogram
hist(total_steps_by_date$steps, col=2, main="Histogram of total number of steps in a day",xlab="")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

### Calculate and report the mean and median of the total number of steps taken per day


```r
mean(total_steps_by_date$steps)
```

```
## [1] 10766.19
```

```r
median(total_steps_by_date$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?


### Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
total_steps_by_interval <- aggregate(steps ~ interval, activity, mean)

plot(total_steps_by_interval$steps, type = "h", col = "red", ylab="Mean", xlab="Interval" )
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

```r
mean(total_steps_by_interval$steps)
```

```
## [1] 37.3826
```

### Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
total_steps_by_interval[which.max(total_steps_by_interval$steps),]
```

```
##     interval    steps
## 104      835 206.1698
```

## Imputing missing values

### Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
nrow(activity[!complete.cases(activity),])
```

```
## [1] 2304
```

### Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.


```r
activity_wihout_NA <- activity
meanSteps <- mean(total_steps_by_date$steps) 
# we change all data NA by daily mean
```

### Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
activity_wihout_NA[is.na(activity$steps), ]$steps <- (meanSteps/288) 
```

### Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
total_steps_by_date_noNA <- aggregate(steps ~ date, activity, sum)

# create histogram
hist(total_steps_by_date_noNA$steps, col=3, main="Histogram of total number of steps in a day",xlab="")
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png) 

```r
mean(total_steps_by_date_noNA$steps)
```

```
## [1] 10766.19
```

```r
median(total_steps_by_date_noNA$steps)
```

```
## [1] 10765
```

## Are there differences in activity patterns between weekdays and weekends?

### Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.


```r
activity$date <- as.Date(activity$date, "%Y-%m-%d")
activity$day <- weekdays(activity$date)
# date in french because I am french :
activity$typeDay <- as.factor(ifelse(weekdays(activity$date) %in% c("samedi", "dimanche"), "weekend", "weekday"))
```

### Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
total_steps_by_typeday <- aggregate(steps ~ interval+typeDay, activity, mean)

# make the panel plot for weekdays and weekends
library(ggplot2)

qplot(interval, steps, data=total_steps_by_typeday, geom=c("line"), xlab="Interval", 
      ylab="Number of steps", main="") + facet_wrap(~ typeDay, ncol=1)
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png) 

```r
ggplot(total_steps_by_typeday, aes(x=interval, y=steps )) + 
 geom_bar(stat="identity") + 
 facet_wrap(~typeDay,ncol=1) + 
 labs(x="Interval", y=expression("Number of Steps"))
```

![](PA1_template_files/figure-html/unnamed-chunk-12-2.png) 
