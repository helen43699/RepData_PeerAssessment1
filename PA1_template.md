#Reproducible Research: Project 1



##Loading and preprocessing the data

1: Load the data (i.e. read.csv())


```r
# data file has been download to work directory
data = read.csv("activity.csv")
```

2: Process/transform the data (if necessary) into a format suitable for your analysis


```r
#convert date factor to date format
data_d <- transform(data, date=as.Date(date, format="%Y-%m-%d"))
```

##What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

1: Calculate the total number of steps taken per day

```r
##aggregate by date and calculate sum
data_agg <- aggregate(steps ~ date, FUN=sum, data=data_d)
```

2: you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day


```r
hist(data_agg$steps,xlab="Number of Steps", main="Total Number of Steps Taken Each Day")
```

![plot of chunk hist](figure/hist-1.png)

3: Calculate and report the mean and median of the total number of steps taken per day


```r
v_mean = mean(data_agg$steps)
v_median = factor(median(data_agg$steps))
print(v_mean)
```

```
## [1] 10766.19
```

```r
print(v_median)
```

```
## [1] 10765
## Levels: 10765
```

##What is the average daily activity pattern?

1: Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
data_agg_i = aggregate(steps~interval, FUN = mean, data = data_d)

plot(x=data_agg_i$interval,
     y=data_agg_i$steps,
     type = "l",
     main = "Average number of steps taken across all days",
     xlab = "Interval",
     ylab = "Steps")
```

![plot of chunk unnamed-chunk-1](figure/unnamed-chunk-1-1.png)

2: Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
data_agg_i[data_agg_i$steps == max(data_agg_i$steps),]
```

```
##     interval    steps
## 104      835 206.1698
```

##Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1: Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
v_na_sum <- sum(is.na(data_d$steps))

print(v_na_sum)
```

```
## [1] 2304
```

2: Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.


```r
# data_agg_i can be used for the mean for 5 minute interval
```

3: Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
#merge original and mean per interval dataset
data_merge = merge(x=data_d, y=data_agg_i, by = "interval")

# replace NA at column 2 with mean at column 4
data_merge[is.na(data_merge[,2]),2] = data_merge[is.na(data_merge[,2]),4] 
```

4: Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
#total number of steps taken each day
data_merge_sum = aggregate(steps.x ~ date, FUN=sum, data=data_merge)
```
  
hist(data_merge_sum$steps.x,xlab="Number of Steps", main="Total Number of Steps Taken Each Day")

#Calculate and report the mean and median total number of steps taken per day
merge_mean = mean(data_merge_sum)
print(merge_mean)

merge_median = factor(median (data_merge_sum))
print(merge_median)
```

Those values are differ from the estimates from the first part of the assignment. In the first part of the assignment, the median was 10765, but after replacing NAs, the median is 10766.19.

##Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1: Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
data_merge$dateType = as.factor(ifelse(weekdays(data_merge$date) %in% c("Saturday", "Sunday"), "weekend", "weekday"))
```

2: Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
# load lattice package
library(lattice)

# calculate mean across all weekdays or weekends
data_agg_week = aggregate(steps.x~interval+dateType, FUN = mean, data = data_merge)

# make plot
xyplot(steps.x ~ interval | dateType, data_agg_week
     , type = "l"
     , xlab = "Interval"
     , ylab = "Number of Steps"
     , main = "Average Number of Steps Taken Across All Weekday Days or Weekend Days"
     , layout = c(1, 2))
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png)
