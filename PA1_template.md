---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data

**Code to download and unzip the data and load activity.csv into R:**

```r
fileUrl <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
download.file(fileUrl, destfile = "Factivity.zip", method = "curl")
unzip("Factivity.zip")
activity <- read.csv("activity.csv")
activity$date <- as.Date(activity$date, "%Y-%m-%d")
```

## What is mean total number of steps taken per day?

**Code to calculate the sum of steps taken for each day:**

```r
sum_steps <- sapply(split(activity$steps, activity$date), sum)
```

**Histogram of the total number of steps per day:**

```r
hist(sum_steps, breaks = 30, xlab = "Steps", main = "Total number of steps per day")
```

![](PA1_template_files/figure-html/plot1-1.png)<!-- -->

**Code to calculate the mean and median number of steps taken per day across all days:**

```r
meansteps <- mean(sum_steps, na.rm = TRUE)
mediansteps <- median(sum_steps, na.rm = TRUE)
```
The mean number of steps per day is 1.0766189\times 10^{4}. 
The median number of steps per day is 10765.

## What is the average daily activity pattern?

**Code to calculate the average number of steps per 5-minute interval across all days:**

```r
interval <- sapply(split(activity$steps, activity$interval), mean, na.rm=TRUE)
```

**Time series plot of the average number of steps taken per 5-minute interval, averaged across all days:**

```r
plot(names(interval), interval, type = "l", xlab = "Interval", ylab = "Average number of steps")
```

![](PA1_template_files/figure-html/plot2-1.png)<!-- -->

**Code to find the 5-minute interval containing the maximum number of steps (on average across all the days in the dataset):**

```r
maxint <- names(which(interval==max(interval)))
```
The maximum number of steps was taken during interval 835.

## Imputing missing values

**Code to calculate the total number of missing values (NAs) in the dataset:**

```r
nasum <- sum(is.na(activity$steps))
```
The number of NAs in the data is 2304.

**Code to impute missing values:**

The mean for the particular 5-minute interval across all days was used as a strategy to fill in missing values.

```r
activity_filled <- activity
activity_filled$averages <- sapply(split(activity_filled$steps, activity_filled$interval), mean, na.rm=TRUE)
replacements <- subset(activity_filled, is.na(activity_filled$steps))$averages
activity_filled$steps <- replace(activity_filled$steps, is.na(activity_filled$steps), replacements)
```

**Code to calculate the sum of steps taken for each day for imputed data: **

```r
sum_steps2 <- sapply(split(activity_filled$steps, activity_filled$date), sum)
```

**Histogram of the total number of steps per day for imputed data: **

```r
hist(sum_steps2, breaks = 30, xlab = "Steps", main = "Total number of steps per day")
```

![](PA1_template_files/figure-html/plot3-1.png)<!-- -->

**Code to calculate the mean and median number of steps taken per day across all days:**

```r
meansteps2 <- mean(sum_steps2, na.rm = TRUE)
mediansteps2 <- median(sum_steps2, na.rm = TRUE)
```
The mean number of steps per day is 1.0766189\times 10^{4}. 
The median number of steps per day is 1.0766189\times 10^{4}.

## Are there differences in activity patterns between weekdays and weekends?

**Code to create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day:**

```r
activity_filled$days <- as.factor(weekdays(activity_filled$date))
levels(activity_filled$days) <- c("weekday","weekday","weekend","weekend","weekday","weekday","weekday")
```

**Code to calculate the average number of steps across all weekday days and weekend days:**

```r
library(dplyr)
```

```
## Warning: package 'dplyr' was built under R version 3.4.2
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
weekdays_steps <- activity_filled %>% group_by(interval, days) %>% summarise(mean_steps = mean(steps))
```

**Panel plot containing a time series plot of the 5-minute interval and the average number of steps taken, averaged across all weekday days or weekend days:**

```r
library(lattice)
xyplot(mean_steps ~ interval | days, data = weekdays_steps, layout = c(1,2), type = "l", xlab = "Interval", ylab = "Average number of steps per day")
```

![](PA1_template_files/figure-html/plot4-1.png)<!-- -->

