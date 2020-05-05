---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---

#### Add and commit the figure/ directory to your git repository.

```r
# dir.create("./figure/")
```

#### Unzipping activity.zip

```r
# unzip("activity.zip")
```


## Loading and preprocessing the data

#### 1. Load the data (i.e. read.csv())

```r
data <- read.csv("activity.csv", header = T)
```

```
## [1] "steps"    "date"     "interval"
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```



## What is mean total number of steps taken per day?

#### 1. Make a histogram of the total number of steps taken each day 

```r
stepsPerDay <- tapply(data$steps, data$date, sum, na.rm = T)
hist(stepsPerDay, xlab = "Number of steps", ylab = "Frequency", main = "Total number of steps per day", col = "gray")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

#### 2. Calculate and report the mean and median total number of steps taken per day


```r
meanStepsPerDay <- mean(stepsPerDay)
meanStepsPerDay
```

```
## [1] 9354.23
```

```r
medianStepsPerDay <- median(stepsPerDay)
medianStepsPerDay
```

```
## [1] 10395
```




## What is the average daily activity pattern?

##### 1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
library(ggplot2)
meanSteps <- aggregate(steps ~ interval, data, FUN = mean)
ggplot(meanSteps, aes(interval, steps)) +
    xlab("5-minute interval") + 
    ylab("Average steps taken across all days") + 
    ggtitle("Average daily activity pattern") +
    geom_line(color='darkblue')
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->





#### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
maxSteps <- meanSteps[which.max(meanSteps$steps), ]
maxSteps
```

```
##     interval    steps
## 104      835 206.1698
```



## Imputing missing values

#### 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
missingValues <- is.na(data$steps)
sum(missingValues)
```

```
## [1] 2304
```



#### 2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

#### The strategy used is the mean, merging points two and three into one.

#### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
library(Hmisc)
```

```
## Loading required package: lattice
```

```
## Loading required package: survival
```

```
## Loading required package: Formula
```

```
## 
## Attaching package: 'Hmisc'
```

```
## The following objects are masked from 'package:base':
## 
##     format.pval, units
```

```r
newData <- data
newData[ , 1] <- impute(data[ , 1], FUN = mean) 
# str(newData)
sum(is.na(newData))
```

```
## [1] 0
```


#### 4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
newData <- tapply(newData$steps, newData$date, sum)
hist(newData, xlab = "Number of steps", ylab = "Frequency", main = "Total number of steps per day", col = "blue")
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png)<!-- -->


```r
meanStepsPerDayNew <- mean(newData)
meanStepsPerDayNew
```

```
## [1] 9354.23
```


```r
medianStepsPerDayNew <- median(newData)
medianStepsPerDayNew
```

```
## [1] 10395
```


```r
meanStepsPerDayNew - meanStepsPerDay
```

```
## [1] 0
```



```r
medianStepsPerDayNew - medianStepsPerDay
```

```
## [1] 0
```

#### The mean and median have not changed due to the imputed values.



## Are there differences in activity patterns between weekdays and weekends?

#### 1. Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
library(timeDate)
data$dayType <- ifelse(isWeekday(data$date) == TRUE, "weekday", "weekend")
str(data)
```

```
## 'data.frame':	17568 obs. of  4 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
##  $ dayType : chr  "weekday" "weekday" "weekday" "weekday" ...
```



#### 2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was created using simulated data:


```r
activityPatterns <- aggregate(steps ~ interval + dayType, data, FUN = mean)
ggplot(activityPatterns, aes(interval, steps)) +
  labs(x = "5-minute interval", y = "Number of steps") +
  ggtitle("Average number of steps across all weekday or weekend") +
  geom_line() +
  facet_grid( dayType ~ .)
```

![](PA1_template_files/figure-html/unnamed-chunk-18-1.png)<!-- -->



