loading data

```r
data <- read.csv(unzip("repdata-data-activity.zip"))
```

```
## Warning: error 1 in extracting from zip file
```

```
## Error: invalid 'description' argument
```

create histogram of total number of steps taken each day

```r
stepsPerDay <- tapply(data$steps, data$date, sum)
hist(stepsPerDay, main="Steps Per Day", xlab="# Steps per Day")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 

calculate mean and median total number of steps taken each day

```r
mean(stepsPerDay, na.rm=TRUE)
```

```
## [1] 10766
```

```r
median(stepsPerDay, na.rm=TRUE)
```

```
## [1] 10765
```

create time series plot

```r
stepsInt <- tapply(data$steps, data$interval, mean, na.rm = T)
interval <- as.numeric(levels(factor(data$interval)))
plot(interval, stepsInt, type = "l", main = "Average Daily Activity Patterns", ylab="Avg # of Steps")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 

find which 5 min interval contains max # of steps

```r
info <- data.frame(interval, stepsInt)
maximum <- info[info[, 2] == max(stepsInt), ][1]
maximum
```

```
##     interval
## 835      835
```

calculate total # of missing vals in dataset

```r
numMissing <- sum(as.numeric(is.na(data$steps)))
numMissing
```

```
## [1] 2304
```

fill in missing vals of dataset with mean for that 5-min interval

```r
filled <- data
for (i in 1:nrow(filled)) {
  if (is.na(filled[i, 1])) 
    filled[i, 1] <- info[info[, 1] == filled[i, 3], ][, 2]
}
```

histogram of total number of steps taken each day

```r
stepFilled <- tapply(filled$steps, filled$date, sum)
hist(stepFilled,  main = "Total Number of Steps per Day", xlab = "Steps per Day", ylab = "Frequency")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 

calculate mean and median of total number of steps taken per day

```r
mean(stepFilled)
```

```
## [1] 10766
```

```r
median(stepFilled)
```

```
## [1] 10766
```
^^ These values do not differ from the estimated part of the assignment

looking for difference in activity patterns btw weekdays and weekends
create new factor var in dataset (weekday and weekend)

```r
daytype <- function(date) {
  if (weekdays(as.Date(date)) %in% c("Saturday", "Sunday")) {
    "Weekend"
  } else {
    "Weekday"
  }
}
data$daytype <- as.factor(sapply(data$date, daytype))
```

create panel plot of 5 min interval and avg # of steps taken, averaged across all weekdays or weekends

```r
for (type in c("Weekend", "Weekday")) {
  stepFilled.type <- aggregate(steps ~ interval, data = data, subset = data$daytype == 
                            type, FUN = mean)
  plot(stepFilled.type, type = "l", main = type)
}
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-111.png) ![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-112.png) 
