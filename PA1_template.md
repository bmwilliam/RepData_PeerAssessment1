---
title: 'Reproducible Research: Peer Assessment 1'
author: "Byungman Choi"
date: "26/07/2021"
output: 
        html_document:
                keep_md: true
---




## Loading and preprocessing the data

### 1. Load the data


```r
step_data <- read.csv("data/activity.csv")
```

### 2. Process/transform the data (if necessary)


```r
step_data$date <- as.Date(step_data$date, "%Y-%m-%d")
#good <- complete.cases(step_data)
#step_data <- step_data[good,]
```


## What is mean total number of steps taken per day?

### 1. Calculate the total number of steps taken per day


```r
totalsteps_perday <- aggregate(steps ~ date, step_data, sum)
```

### 2. Make a histogram of the total number of steps taken per day


```r
library(ggplot2)
qplot(steps, data = totalsteps_perday,main = "Total steps per day",xlab = "Day",ylab = "Freq")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](PA1_template_files/figure-html/histogramtotal-1.png)<!-- -->

### 3. Calculate and report the mean and median of the total number of steps taken per day

The mean of the total number of steps taken per day:

```r
mean(totalsteps_perday$steps)
```

```
## [1] 10766.19
```

The median of the total number of steps taken per day:

```r
median(totalsteps_perday$steps)
```

```
## [1] 10765
```
## What is the average daily activity pattern?

### 1. Make a time series plot of the 5 min interval and the average number of steps taken, averaged across all days.


```r
time_series <- tapply(step_data$steps,step_data$interval,mean, na.rm=TRUE)
df_time_series <- data.frame(key=row.names(time_series),value=time_series)
df_time_series$key <- as.numeric(df_time_series$key)

ggplot(df_time_series,aes(key,value)) + geom_line(color="red") + geom_point()
```

![](PA1_template_files/figure-html/timeseriesplot-1.png)<!-- -->

### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
max_value <- which.max(df_time_series$value)
df_time_series$key[max_value]
```

```
## [1] 835
```

## Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

### 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
summary(step_data$steps)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
##    0.00    0.00    0.00   37.38   12.00  806.00    2304
```

```r
sum(is.na(step_data$steps))
```

```
## [1] 2304
```

### 2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

Replace NAs with mean values of steps.

### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
no_NA_step_data <- step_data
no_NA_step_data$steps[is.na(no_NA_step_data$steps)] <- mean(no_NA_step_data$steps,na.rm=TRUE)
```

### 4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
no_NA_totalsteps_perday <- aggregate(steps ~ date, no_NA_step_data, sum)
qplot(steps, data = no_NA_totalsteps_perday,main = "Total steps per day (no NAs)",xlab = "Day",ylab = "Freq")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](PA1_template_files/figure-html/no_NA_meansteps-1.png)<!-- -->

The mean of the total number of steps (no NAs) taken per day:

```r
mean(no_NA_totalsteps_perday$steps)
```

```
## [1] 10766.19
```

The median of the total number of steps (no NAs) taken per day:

```r
median(no_NA_totalsteps_perday$steps)
```

```
## [1] 10766.19
```

#### There are no significant changes in the mean and median between original and imputed data. The median values are slightly different due to the impact of imputing missing data. Original median = 10765 vs. median after imputing missing values = 10766.19.


## Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

### 1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.


```r
no_NA_step_data$weekendday <- factor(chron::is.weekend(no_NA_step_data$date),labels = c("weekday","weekend"))
```

### 2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
ggplot(no_NA_step_data,aes(interval,steps))+geom_line()+facet_wrap(no_NA_step_data$weekendday,nrow=2,ncol=1)+labs(y="Number of steps")
```

![](PA1_template_files/figure-html/panelplot-1.png)<!-- -->
