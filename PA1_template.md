Course 5 Project 1
==================

This is the requested report for assignment.

Setting the libraries

    library(dplyr)
    library(ggplot2)

Loading and preprocessing the data
----------------------------------

Show any code that is needed to

-   Load the data (i.e. 𝚛𝚎𝚊𝚍.𝚌𝚜𝚟())
-   Process/transform the data (if necessary) into a format suitable for
    your analysis

<!-- -->

    a <- read.csv("activity.csv")
    a$date <- as.Date(a$date, "%Y-%m-%d")

What is mean total number of steps taken per day?
-------------------------------------------------

-   Calculate the total number of steps taken per day

<!-- -->

    a.sum <- summarise(group_by(a[!is.na(a$steps),], date), sum(steps))
    names(a.sum) <- c("date", "qty_steps")
    a.sum

    ## # A tibble: 53 × 2
    ##          date qty_steps
    ##        <date>     <int>
    ## 1  2012-10-02       126
    ## 2  2012-10-03     11352
    ## 3  2012-10-04     12116
    ## 4  2012-10-05     13294
    ## 5  2012-10-06     15420
    ## 6  2012-10-07     11015
    ## 7  2012-10-09     12811
    ## 8  2012-10-10      9900
    ## 9  2012-10-11     10304
    ## 10 2012-10-12     17382
    ## # ... with 43 more rows

-   If you do not understand the difference between a histogram and a
    barplot, research the difference between them. Make a histogram of
    the total number of steps taken each day

<!-- -->

    ggplot(a.sum, aes(x = qty_steps)) + geom_histogram( colour = "grey1")

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-4-1.png)

-   Calculate and report the mean and median of the total number of
    steps taken per day

<!-- -->

    mean(a.sum$qty_steps)

    ## [1] 10766.19

    median(a.sum$qty_steps)

    ## [1] 10765

What is the average daily activity pattern?
===========================================

-   Make a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute
    interval (x-axis) and the average number of steps taken, averaged
    across all days (y-axis)

<!-- -->

    a.mean <- summarise(group_by(a[!is.na(a$steps),], interval), mean(steps))
    names(a.mean) <- c("interval", "mean_steps")
    ggplot(a.mean, aes(x=interval, y=mean_steps)) + geom_line()

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-6-1.png)

-   Which 5-minute interval, on average across all the days in the
    dataset, contains the maximum number of steps?

<!-- -->

    a.mean[which.max(a.mean$mean_steps),]

    ## # A tibble: 1 × 2
    ##   interval mean_steps
    ##      <int>      <dbl>
    ## 1      835   206.1698

Imputing missing values
=======================

-   Calculate and report the total number of missing values in the
    dataset (i.e. the total number of rows with 𝙽𝙰s)

<!-- -->

    a.rm <- a[is.na(a$steps),]
    nrow(a.rm)

    ## [1] 2304

-   Devise a strategy for filling in all of the missing values in
    the dataset. The strategy does not need to be sophisticated. For
    example, you could use the mean/median for that day, or the mean for
    that 5-minute interval, etc.

<!-- -->

    a.nas <- is.na(a$steps)
    a.avg <- tapply(a$steps, a$interval, mean, na.rm=TRUE, simplify = TRUE)

-   Create a new dataset that is equal to the original dataset but with
    the missing data filled in.

<!-- -->

    b <- a
    b$steps[a.nas] <- a.avg[as.character(a$interval[a.nas])]
    b.sum <- summarise(group_by(b, date), qty_steps = sum(steps))

-   Make a histogram of the total number of steps taken each day and
    Calculate and report the mean and median total number of steps taken
    per day. Do these values differ from the estimates from the first
    part of the assignment? What is the impact of imputing missing data
    on the estimates of the total daily number of steps?

<!-- -->

    mean(b.sum$qty_steps)

    ## [1] 10766.19

    median(b.sum$qty_steps)

    ## [1] 10766.19

Are there differences in activity patterns between weekdays and weekends?
=========================================================================

-   Create a new factor variable in the dataset with two levels –
    “weekday” and “weekend” indicating whether a given date is a weekday
    or weekend day.

<!-- -->

    weekend <- format(b$date, "%u") %in% c(6, 7)
    b$weekday[weekend] <- "weekend"
    b$weekday[!weekend] <- "weekday"
    b$weekday <- as.factor(b$weekday)

-   Make a panel plot containing a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of
    the 5-minute interval (x-axis) and the average number of steps
    taken, averaged across all weekday days or weekend days (y-axis).
    See the README file in the GitHub repository to see an example of
    what this plot should look like using simulated data.

<!-- -->

    b.mean <- summarise(group_by(b, interval, weekday), mean_steps = mean(steps))
    ggplot(b.mean, aes(x=interval, y=mean_steps, colour = weekday)) + geom_line() + facet_wrap(~weekday, ncol = 1, nrow = 2)

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-13-1.png)
