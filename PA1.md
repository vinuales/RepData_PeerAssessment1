# Reproducible Research: Peer Assessment 1
David V.  
5 de junio de 2016  

## Loading and preprocessing the data

- Load the data (i.e. read.csv())

```r
#Using datasep from repo
zipFile = "activity.zip"
dataFile = "activity.csv"

if(!file.exists(dataFile)){
  unzip(zipFile)
}
activityData <- read.csv(dataFile)
```

- Process/transform the data (if necessary) into a format suitable for your analysis

No extra process/transform needed.

## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

- Calculate the total number of steps taken per day

```r
stepsData <- aggregate(steps ~ date, data=activityData, sum)
```

- If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day

```r
hist(stepsData$steps)
```

![](PA1_files/figure-html/histSteps-1.png)<!-- -->

- Calculate and report the mean and median of the total number of steps taken per day

```r
meanStepsData <- mean(stepsData$steps)
medianStepsData <- median(stepsData$steps)
```
Mean is 1.0766189\times 10^{4} and Median is 10765.

## What is the average daily activity pattern?

- Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
intervalData <- aggregate(steps~interval ,data=activityData, mean)
plot(intervalData, type = "l")
```

![](PA1_files/figure-html/intervalSteps-1.png)<!-- -->

- Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
maxInterval <- intervalData$interval[which.max(intervalData$steps)]
```
Max steps interval is 835.

## Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

- Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
naTotal <- sum(is.na(activityData))
naSteps <- sum(is.na(activityData$steps))
naInterval <- sum(is.na(activityData$interval))
naDate <- sum(is.na(activityData$date))
```
Total NA's are 2304, all of them located on steps column.

- Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

I will fill the NA's with the previously calculated interval-steps mean.

- Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
newActivityData <- activityData
for(i in 1:nrow(newActivityData)){
  if(is.na(newActivityData$steps[i])){
    newActivityData$steps[i] <- intervalData$steps[intervalData$interval==newActivityData$interval[i]]
  }
}
```
- Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
newStepsData <- aggregate(steps ~ date, data=newActivityData, sum)
hist(newStepsData$steps)
```

![](PA1_files/figure-html/newStepsData-1.png)<!-- -->

```r
meanNew <- mean(newStepsData$steps)
medianNew <- median(newStepsData$steps)
```
Mean value stays the same (1.0766189\times 10^{4}), but there's a little differente in Median, where now equals Mean value (1.0766189\times 10^{4}). 

## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

- Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
weekActivityData <- activityData
for(i in 1:nrow(weekActivityData)){
  if(weekdays(as.Date(weekActivityData$date[i])) %in% c('sabado', 'domingo')){
    weekActivityData$week[i] <- "weekend"
  } else {
    weekActivityData$week[i] <- "weekday"
  }
}
```
  Note (Spanish translation): Sabado=Saturday, Domingo=Sunday

- Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
weekStepsData = aggregate(steps ~ interval + week, weekActivityData, mean)
library(lattice)
xyplot(steps ~ interval|factor(week), data=weekStepsData, aspect=1/2,type="l")
```

![](PA1_files/figure-html/weekStepsData-1.png)<!-- -->
