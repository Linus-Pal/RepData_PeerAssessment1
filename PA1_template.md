##Reproducible Research: Peer Assessment #1

##Load required packages
```{r, echo=TRUE}

library(lattice)
library(ggplot2)
library(knitr)
library(markdown)

```
##Loading and preprocessing the data

```{r, echo=TRUE}
activity <- read.csv("C:/Users/Sunil/Desktop/datasciencecoursera/Reproducible Research/data/activity.csv", colClasses = c("numeric", "character","numeric"))
head(activity)

```
```{r, echo=TRUE}
names(activity)

```
```{r, echo=TRUE}
activity$date <- as.Date(activity$date, "%Y-%m-%d")

```
#What is mean total number of steps taken per day?

```{r, echo=TRUE}
StepsTotal <- aggregate(steps ~ date, data = activity, sum, na.rm = TRUE)

```
## The Histogram is

```{r, echo=TRUE}
hist(StepsTotal$steps, main = "Total steps by day", xlab = "day", col = "blue")
```

## mean and median is

```{r, echo=TRUE}
mean(StepsTotal$steps)
```
```{r, echo=TRUE}
median(StepsTotal$steps)
```

#What is the average daily activity pattern?

### Make a time series plot (i.e. type = �l�) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

### Plot for mean of steps and time series

```{r, echo=TRUE}
timeseries <- tapply(activity$steps, activity$interval, mean, na.rm = TRUE)
```
## The plot is

```{r, echo=TRUE}
plot(row.names(timeseries), timeseries, type = "l", xlab = "5-min interval", 
    ylab = "Average across all Days", main = "Average number of steps taken", 
    col = "red")
```

### Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```{r, echo=TRUE}
max_interval <- which.max(timeseries)
names(max_interval)
```
#Imputing missing values

## Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```{r, echo=TRUE}
activityNA <- sum(is.na(activity))
activityNA
```
### Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval,etc

```{r, echo=TRUE}
StepsAverage <- aggregate(steps ~ interval, data = activity, FUN = mean)
fillNA <- numeric()
for (i in 1:nrow(activity)) { obs <- activity[i, ]
    if (is.na(obs$steps)) {steps <- subset(StepsAverage, interval == obs$interval)$steps
    } else
         {steps <- obs$steps}
    fillNA <- c(fillNA, steps)
}
```
## Create a new dataset that is equal to the original dataset but with the missing data filled in.

```{r, echo=TRUE}
new_activity <- activity
new_activity$steps <- fillNA
```
### Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```{r, echo=TRUE}
StepsTotalnew <- aggregate(steps ~ date, data = new_activity, sum, na.rm = TRUE)
```
## The Histogram is

```{r, echo=TRUE}
hist(StepsTotalnew$steps, main = "Total steps by day", xlab = "day", col = "blue")
```

## mean and median are

```{r, echo=TRUE}
mean(StepsTotalnew$steps)
```

```{r, echo=TRUE}
median(StepsTotalnew$steps)
```
### After replacing the mean is the same but the median is a slightly different


#Are there differences in activity patterns between weekdays and weekends?

### For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

### 1.Create a new factor variable in the dataset with two levels ? ?weekday? and ?weekend? indicating whether a given date is a weekday or weekend day.

```{r, echo=TRUE}
day <- weekdays(activity$date)
daylevel <- vector()
for (i in 1:nrow(activity)) {
    if (day[i] == "Saturday") {
        daylevel[i] <- "Weekend"
    } else if (day[i] == "Sunday") {
        daylevel[i] <- "Weekend"
    } else {
        daylevel[i] <- "Weekday"
    }
}
activity$daylevel <- daylevel
activity$daylevel <- factor(activity$daylevel)

stepsByDay <- aggregate(steps ~ interval + daylevel, data = activity, mean)
names(stepsByDay) <- c("interval", "daylevel", "steps")
```
### 2.Make a panel plot containing a time series plot (i.e. type = �l�) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). 
```{r, echo=TRUE}
xyplot(steps ~ interval | daylevel, stepsByDay, type = "l", layout = c(1, 2),xlab = "Interval", ylab = "Number of steps")
```
```{r, echo=TRUE}
setwd("C:/Users/Sunil/Desktop/datasciencecoursera/Reproducible Research/data")
```
```{r, echo=TRUE}
knit2html(input="PA1_template.Rmd", output="PA1_template.html")
```