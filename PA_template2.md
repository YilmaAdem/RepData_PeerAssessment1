Reproducible Research :Activity Monitoring Data Assignment
==========================================================

    ###opts_chunk$set(echo=TRUE, results = 'hold')

get the packages
================

    library(ggplot2)

    ## Warning: package 'ggplot2' was built under R version 3.3.2

    library(scales)

    ## Warning: package 'scales' was built under R version 3.3.2

    library(knitr)

    ## Warning: package 'knitr' was built under R version 3.3.2

    library(Hmisc)

    ## Warning: package 'Hmisc' was built under R version 3.3.3

    ## Loading required package: lattice

    ## Loading required package: survival

    ## Warning: package 'survival' was built under R version 3.3.3

    ## Loading required package: Formula

    ## Warning: package 'Formula' was built under R version 3.3.2

    ## 
    ## Attaching package: 'Hmisc'

    ## The following objects are masked from 'package:base':
    ## 
    ##     format.pval, round.POSIXt, trunc.POSIXt, units

    library(data.table)

    ## Warning: package 'data.table' was built under R version 3.3.2

    library(lattice)
    opts_chunk$set(echo=TRUE, results = 'hold')

Question 1. Load CVS data and looks the summary

    activityD <- read.csv("activity.csv")
    head(activityD)

    ##   steps       date interval
    ## 1    NA 2012-10-01        0
    ## 2    NA 2012-10-01        5
    ## 3    NA 2012-10-01       10
    ## 4    NA 2012-10-01       15
    ## 5    NA 2012-10-01       20
    ## 6    NA 2012-10-01       25

Question 2.Histogram of the total number of steps taken each day. \*
Create a dataframe using tapply() that sums the steps for each day. \*
Make the histogram

    SumstepsByDay <- aggregate(steps ~ date, activityD, sum, na.rm=TRUE)
    colnames(SumstepsByDay) <- c("date", "steps")
    ggplot(SumstepsByDay, aes(x = steps)) + geom_histogram(fill = "green", binwidth = 1500) + labs(main="Sum of Steps taken each Day", x = "Number of Steps", y = "Days") + theme_bw()

![](PA_template2_files/figure-markdown_strict/histogram-1.png)

Question 3. Calculate Mean and Median number of steps taken each day

    stepsByDayMean <- mean(SumstepsByDay$steps, na.rm = TRUE)
    stepsByDayMedian <- median(SumstepsByDay$steps, na.rm = TRUE)
    stepsByDayMean
    stepsByDayMedian

    ## [1] 10766.19
    ## [1] 10765

Mean: r stepsByDayMean Median: r stepsByDayMedian

Question 4. Time series plot of the average number of steps taken \*Make
a time series plot (i.e. type = "l") of the 5-minute interval (x-axis)
and the average number of steps taken, averaged across all days (y-axis)

    stepseach_interval <- aggregate(steps ~ interval, activityD, mean)
    plot(stepseach_interval$interval, stepseach_interval$steps, xlab ="interval", ylab ="Steps", main = "Average Number of steps taken by interval")

![](PA_template2_files/figure-markdown_strict/timeseries-1.png) Question
5. Which 5-minute interval, on average across all the days in the
dataset, contains the maximum number of steps?

    maxNumSteps <- stepseach_interval[which.max(stepseach_interval$steps),1]
    maxNumSteps

    ## [1] 835

Question 6. Code to Describe and show a strategy for imputing missing
data Note that there are a number of days/intervals where there are
missing values (coded as NA). The presence of missing days may introduce
bias into some calculations or summaries of the data.

6.1. Calculate and report the total number of missing values in the
dataset (i.e. the total number of rows with NAs)

    missingV <- sum(!complete.cases(activityD))
    missingV

    ## [1] 2304

6.2.Devise a strategy for filling in all of the missing values in the
dataset. The strategy does not need to be sophisticated. For example,
you could use the mean/median for that day, or the mean for that
5-minute interval, etc.

    missing_value <- sum(is.na(activityD$steps))
    missing_value

    ## [1] 2304

6.3.Create a new dataset that is equal to the original dataset but with
the missing data filled in.

    fill_na <- activityD
    fill_na$steps <- impute(fill_na$steps, fun=mean)
    sum(is.na(fill_na$steps))

    ## [1] 0

6.4.Make a histogram of the total number of steps taken each day and
Calculate and report the mean and median total number of steps taken per
day. Do these values differ from the estimates from the first part of
the assignment? What is the impact of imputing missing data on the
estimates of the total daily number of steps

    TotalStepsNA <- aggregate(steps ~ date, fill_na, sum)
    hist(TotalStepsNA$steps, main = paste("Total Steps each day"), col = "blue", xlab = "Steps(Imputed)")

![](PA_template2_files/figure-markdown_strict/unnamed-chunk-6-1.png)

Caculate and report the Mean

    rmeantotal <- mean(TotalStepsNA$steps)
    rmeantotal

    ## [1] 10766.19

Caculate and report the Median

    rmediantotal <- median(TotalStepsNA$steps)
    rmediantotal

    ## [1] 10766.19

Do these values differ from the estimates from the first part of the
assignment? What is the impact of imputing missing data on the estimates
of the total daily number of steps

    rmeandiff <- rmeantotal - stepsByDayMean
    rmeandiff

    rmediandiff <- rmediantotal - stepsByDayMedian
    rmediandiff

    ## [1] 0
    ## [1] 1.188679

There is no mean variance for the values but the median has a variance
of 1.188679 between the two values. The impact of missing data is
substantail on 10000.

1.  Panel plot comparing the average number of steps taken per 5-minute
    interval across weekdays and weekends Are there differences in
    activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the
dataset with the filled-in missing values for this part.

Create a new factor variable in the dataset with two levels - "weekday"
and "weekend" indicating whether a given date is a weekday or weekend
day. Make a panel plot containing a time series plot (i.e. type = "l")
of the 5-minute interval (x-axis) and the average number of steps taken,
averaged across all weekday days or weekend days (y-axis).

Comparing differences in accitivity patterns weekends vs weekdays
=================================================================

    fill_na$dateType <- ifelse(as.POSIXlt(fill_na$date)$wday %in% c(0,6), 'weekend', 'weekday')
    Avg_wksfill_na <- aggregate(steps ~ interval + dateType, data=fill_na, mean)
    ggplot(Avg_wksfill_na, aes(interval, steps)) + 
        geom_line() + 
        facet_grid(dateType ~ .) +
        xlab("Interval") + 
        ylab("avarage number of steps")

![](PA_template2_files/figure-markdown_strict/unnamed-chunk-10-1.png)

End of the page
