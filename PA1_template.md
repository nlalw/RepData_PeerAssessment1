# Reproducible Research: Peer Assessment 1

```
## Warning: package 'Hmisc' was built under R version 3.2.3
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
## Warning: package 'Formula' was built under R version 3.2.3
```

```
## Loading required package: ggplot2
```

```
## Warning: package 'ggplot2' was built under R version 3.2.3
```

```
## 
## Attaching package: 'Hmisc'
```

```
## The following objects are masked from 'package:base':
## 
##     format.pval, round.POSIXt, trunc.POSIXt, units
```

## Loading and preprocessing the data


```r
activityCsvData <- read.csv("activity.csv")
```

View(activityCsvData)

## What is mean total number of steps taken per day?


```r
stepsPerDay <- aggregate(steps ~ date, activityCsvData, sum)
head(stepsPerDay)
```

```
##         date steps
## 1 2012-10-02   126
## 2 2012-10-03 11352
## 3 2012-10-04 12116
## 4 2012-10-05 13294
## 5 2012-10-06 15420
## 6 2012-10-07 11015
```

```r
hist(stepsPerDay$steps, main="Total Steps per day", col="red", xlab="Number of Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)

```r
meanSteps <- mean(stepsPerDay$steps)
medianSteps <- median(stepsPerDay$steps)
```
The mean is:

```
## [1] 10766.19
```
The Median is: 

```
## [1] 10765
```

## What is the average daily activity pattern?


```r
dailyActivity <- aggregate(steps ~ interval, activityCsvData, mean)
head(dailyActivity)
```

```
##   interval     steps
## 1        0 1.7169811
## 2        5 0.3396226
## 3       10 0.1320755
## 4       15 0.1509434
## 5       20 0.0754717
## 6       25 2.0943396
```

```r
#Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

plot(dailyActivity, type="l", col="blue", ylab="Steps", xlab="Interval", main="Daily Activity Pattern")
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png)

```r
#Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
maxInterval <- dailyActivity[which.max(dailyActivity$steps), 1]
```
5-minute Interval that contains maximum number of Steps:

```
## [1] 835
```

## Imputing missing values


```r
#Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
naRows <- is.na(activityCsvData)
```
Total number of missing values in dataset is:

```
## [1] 2304
```


```r
#Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

activityCsvData$steps <- impute(activityCsvData$steps, fun=mean)

#Create a new dataset that is equal to the original dataset but with the missing data filled in.
newDataSet <- activityCsvData
View(newDataSet)

#Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

newStepsPerDay <- aggregate(steps ~ date, newDataSet, sum)
head(newStepsPerDay)
```

```
##         date    steps
## 1 2012-10-01 10766.19
## 2 2012-10-02   126.00
## 3 2012-10-03 11352.00
## 4 2012-10-04 12116.00
## 5 2012-10-05 13294.00
## 6 2012-10-06 15420.00
```

```r
hist(newStepsPerDay$steps, main="Total Steps per day", col="red", xlab="Number of Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png)

```r
newMeanSteps <- mean(newStepsPerDay$steps)
newMedianSteps <- median(newStepsPerDay$steps)
```
The new mean is:

```
## [1] 10766.19
```
The new Median is: 

```
## [1] 10766.19
```

## Are there differences in activity patterns between weekdays and weekends?

```r
#Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

newDataSet$WeekDay <- ifelse(weekdays(as.Date(newDataSet$date))==c("Sunday"), "weekend", "weekday")
View(newDataSet)

#Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

weekdayActivity <- aggregate(steps ~ WeekDay+interval, newDataSet, mean)

ggplot(weekdayActivity, aes(interval, steps)) + 
    geom_line(color="blue") + 
    facet_wrap(~ WeekDay,nrow=2,ncol=1) +
    xlab("5-minute interval") + 
    ylab("Number of steps")+theme_light()
```

![](PA1_template_files/figure-html/unnamed-chunk-13-1.png)











