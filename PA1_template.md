# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

```r
unzip("activity.zip")
data <- read.csv("activity.csv")
```


## What is mean total number of steps taken per day?
Calculate the total steps taken per day.

```r
steps <- aggregate(data$steps, list(date=data$date), FUN=sum, na.rm=TRUE)
steps <- steps$x
```
This is the histogram of the total steps per day.  

```r
hist(steps, col="red")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

The mean is:

```r
mean(steps)
```

```
## [1] 9354.23
```
The median is:

```r
median(steps)
```

```
## [1] 10395
```

## What is the average daily activity pattern?
This is a time series plot of the 5 minute interval vs the 
average number of steps.

```r
steps_interval <- aggregate(data$steps, list(interval=data$interval), 
                            FUN=mean, na.rm=TRUE)
with(steps_interval, plot(interval, x, type="l", col="red",
                          xlab="interval", ylab="average number of steps"))
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png) 

The interval which contains the max number of steps is:

```r
steps_interval[which.max(steps_interval$x), "interval"]
```

```
## [1] 835
```

## Imputing missing values
The total number of missing values is:

```r
length(data[is.na(data)])
```

```
## [1] 2304
```
I'll use the mean for that 5-minute interval to fill the missing values

```r
new_data <- data
new_data[is.na(new_data),"steps"] <- steps_interval$x
```
Then, re-plot the histogram for the new dataset:

```r
new_steps <- aggregate(new_data$steps, list(data=new_data$date),
                       FUN=sum)
new_steps <- new_steps$x
hist(new_steps, col="red")
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png) 

And, re-calculate the mean:

```r
mean(new_steps)
```

```
## [1] 10766.19
```
And, re-calculate the median:

```r
median(new_steps)
```

```
## [1] 10766.19
```

Compared to the previous result, they're different! The latter add more weight on
the average level.

## Are there differences in activity patterns between weekdays and weekends?
Let's create an var indicates the date is a weekday or weekend.

```r
Sys.setlocale("LC_TIME", "en_US")
```

```
## [1] "en_US"
```

```r
new_data$week_type <- ifelse(weekdays(as.Date(new_data$date)) %in% c("Saturday", "Sunday"),
                             "weekend", "weekday")
```
And, this is the panel plot:

```r
new_steps_interval <- aggregate(new_data$steps, 
                                list(interval=new_data$interval, week_type=new_data$week_type),
                                FUN=mean)
library(lattice)
xyplot(x ~ interval | week_type, data = new_steps_interval, layout=c(1,2), type="l",
       ylab="Number of steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-14-1.png) 
