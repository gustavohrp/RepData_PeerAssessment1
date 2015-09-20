# Reproducible Research: Peer Assessment 1

## Loading and preprocessing the data

Show any code that is needed to

1. Load the data (i.e. `read.csv()`)
2. Process/transform the data (if necessary) into a format suitable for your analysis


```r
unzip("activity.zip")
activity = read.csv("activity.csv")
```

## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

1. Make a histogram of the total number of steps taken each day


```r
steps.date <- aggregate(steps ~ date, data = activity, FUN = sum, na.rm = TRUE)
hist(steps.date$step) 
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

2. Calculate and report the **mean** and **median** total number of steps taken per day


```r
mean(steps.date$steps)
```

```
## [1] 10766.19
```

```r
median(steps.date$steps)
```

```
## [1] 10765
```

The **mean** total number of steps taken per day is 1.0766189\times 10^{4} steps.
The **median** total number of steps taken per day is 10765 steps.
  
## What is the average daily activity pattern?

1. Make a time series plot (i.e. `type = "l"`) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
steps.interval <- aggregate(steps ~ interval, data = activity, FUN = mean, na.rm = TRUE)
plot(steps.interval, type = "l")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
steps.interval$interval[which.max(steps.interval$steps)]
```

```
## [1] 835
```

It is the **835** interval.

## Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as `NA`). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with `NA`s)


```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```

Total 2304 rows are missing.

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

I will use the means for the 5-minute intervals as fillers for missing values.

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
activity1 <- merge(activity, steps.interval, by="interval", suffixes=c("",".y"))
nas <- is.na(activity1$steps)
activity1$steps[nas] <- activity1$steps.y[nas]
activity1 <- activity1[,c(1:3)]
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the **mean** and **median** total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
steps.date1 <- aggregate(steps ~ date, data=activity1, FUN=sum)
barplot(steps.date1$steps, names.arg=steps.date1$date, xlab="date", ylab="steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png) 

```r
hist(steps.date1$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-8-2.png) 

```r
mean(steps.date1$steps)
```

```
## [1] 10766.19
```

```r
median(steps.date1$steps)
```

```
## [1] 10766.19
```

The **mean** total number of steps taken per day is 1.0766189\times 10^{4} steps.
The **median** total number of steps taken per day is 1.0766189\times 10^{4} steps.

## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
activity1$day = ifelse(as.POSIXlt(as.Date(activity1$date))$wday%%6 == 
    0, "weekend", "weekday")
# For Sunday and Saturday : weekend, Other days : weekday
activity1$day = factor(activity1$day, levels = c("weekday", "weekend"))
```

1. Make a panel plot containing a time series plot (i.e. `type = "l"`) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was created using **simulated data**:


```r
stepsInterval2 = aggregate(steps ~ interval + day, activity1, mean)
library(lattice)
xyplot(steps ~ interval | factor(day), data = stepsInterval2, aspect = 1/2, 
    type = "l")
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png) 
