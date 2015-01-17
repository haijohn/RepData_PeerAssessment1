# Reproducible Research: Peer Assessment 1



### Loading and preprocessing the data


```r
raw <- read.csv('activity.csv')
```

### What is mean total number of steps taken per day?

use tapply to caculate total steps per day

```r
totalStepsPerDay <- tapply(raw$steps,raw$date,sum)
```
make a histogram of total steps per day

```r
hist(totalStepsPerDay)
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

```r
mean(totalStepsPerDay,na.rm=T)
```

```
## [1] 10766.19
```

```r
median(totalStepsPerDay,na.rm=T)
```

```
## [1] 10765
```


### What is the average daily activity pattern?

```r
meanStepsPerInterval <- tapply(raw$steps,raw$interval,mean,na.rm=T)
plot(names(meanStepsPerInterval),meanStepsPerInterval,type='l',xlab='interval(min)',ylab='average steps',main='average steps across all days')
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

```r
meanStepsPerInterval[which.max(meanStepsPerInterval)]
```

```
##      835 
## 206.1698
```

### Imputing missing values

```r
nas <- raw[is.na(raw$steps),]
nrow(nas)
```

```
## [1] 2304
```

```r
imputed <- raw
#inpute NAs with average step of that interval
imputed[is.na(imputed$steps),]$steps <- meanStepsPerInterval[as.character(imputed[is.na(imputed$steps),]$interval)]
```

use tapply to caculate total steps per day

```r
totalStepsPerDay <- tapply(imputed$steps,imputed$date,sum)
```
make a histogram of total steps per day

```r
hist(totalStepsPerDay)
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png) 

```r
mean(totalStepsPerDay,na.rm=T)
```

```
## [1] 10766.19
```

```r
median(totalStepsPerDay,na.rm=T)
```

```
## [1] 10766.19
```


### Are there differences in activity patterns between weekdays and weekends?

```r
date = weekdays(strptime(imputed$date,"%Y-%m-%d"))
week <- ifelse(date %in% c("Sunday","Saturday"),'weekend','weekday')
imputed$week <- week
library(plyr)
weekdayOrweekends <- ddply(imputed,.(interval,week),summarise,meanSteps=mean(steps))
library(ggplot2)
g <- ggplot(weekdayOrweekends)
g+geom_line(aes(x=interval,y=meanSteps))+facet_wrap(~week,nrow=2)
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png) 
