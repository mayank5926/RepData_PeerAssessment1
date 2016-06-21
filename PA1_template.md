# PA1_template.Rmd
Mayank Kumar  
June 21, 2016  



## Loading and preprocessing the data

Show any code that is needed to

1. Load the data (i.e. read.csv())

```r
## Unzip the given data
unzip("repdata_data_activity.zip")
## Read CSV
actdata<-read.csv("activity.csv")
```

2. Process/transform the data (if necessary) into a format suitable for your analysis

```r
## No processing or transformation required at this stage
```

## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

1. Make a histogram of the total number of steps taken each day

```r
## Aggregate number of steps on dates
dailysteps <-aggregate(actdata$steps, by=list(actdata$date),FUN=sum, na.rm=TRUE)
## Provide appropriate names to columns
colnames(dailysteps)<-c("Date","Steps")
## Plot number of steps vs. dates
barplot(dailysteps$Steps,xlab="Date",ylab="Steps", main="Steps Taken vs. Date",names.arg=dailysteps$Date)
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

2. Calculate and report the mean and median total number of steps taken per day

```r
## Calculate mean of number of steps taken per day
mean(dailysteps$Steps)
```

```
## [1] 9354.23
```

```r
## Calculate median of number of steps taken per day
median(dailysteps$Steps)
```

```
## [1] 10395
```
## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
## Average number of steps over intervals
dailyint <-aggregate(actdata$steps, by=list(actdata$interval),FUN=mean, na.rm=TRUE)
## Provide appropriate names to columns
colnames(dailyint)<-c("Interval","Steps")
## Plot average number of steps vs. intervals
plot(dailyint$Interval,dailyint$Steps,type="l",xlab="Interval",ylab="Steps",
        main="Steps Taken vs. Intervals")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
## Print interval number for max number of steps
dailyint[dailyint$Steps==max(dailyint$Steps),"Interval"]
```

```
## [1] 835
```

## Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
## Calculate number of rows which are not complete (i.e. have NA data)
nrow(actdata[!complete.cases(actdata),])
```

```
## [1] 2304
```


2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

```r
## Will replace the mean for that day
```


3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
## Make a copy of data
copydata<-actdata
## Fill the NA values with mean for that day
copydata$steps[is.na(copydata$steps)] = mean(copydata$steps, na.rm=TRUE)
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
## Aggregate number of steps on dates
dailysteps <-aggregate(copydata$steps, by=list(copydata$date),FUN=sum, na.rm=TRUE)
## Provide appropriate names to columns
colnames(dailysteps)<-c("Date","Steps")
## Plot number of steps vs. dates
barplot(dailysteps$Steps,xlab="Date",ylab="Steps", main="Steps Taken vs. Date (Imputed Data)",names.arg=dailysteps$Date)
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

```r
## Calculate mean of number of steps taken per day (imputed data)
mean(dailysteps$Steps)
```

```
## [1] 10766.19
```

```r
## Calculate median of number of steps taken per day (imputed data)
median(dailysteps$Steps)
```

```
## [1] 10766.19
```

```r
## Previously, missing values were ignored completely. But, after imputing NA values have been replaced with positive values (calculated as mean for that day). Hence, naturally the mean and median of number of steps taken has increased over previous case.
```


## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
## Add column for day of week in given data
actdata$day<-weekdays(as.Date(actdata$date))
## Add label for day type - Weekend or Weekday
actdata$daytype<-ifelse(((actdata$day=="Sunday") | (actdata$day == "Saturday")),"Weekend","Weekday")
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). 

```r
## Load ggplot2 library
library(ggplot2)
## Average number of steps over intervals and day type (weeday or weekend)
wdata<-aggregate(actdata$steps,by=list(actdata$interval,actdata$daytype),FUN=mean,na.rm=TRUE)
## Provide appropriate names to columns
colnames(wdata)<-c("Interval","DayType", "Steps")
## Create graph object
g<-ggplot(wdata,aes(Interval,Steps))
## Draw graphs with appropriate facets for weekend and weekdays
g+geom_line()+facet_grid(DayType~.)+labs(title="Steps Taken vs. Intervals for Weekdays and Weekends")
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

