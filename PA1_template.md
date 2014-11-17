# Reproducible Research: Peer Assessment 1
## The questions investigated
This assignment makes use of data from a personal activity monitoring
device. This device collects data at 5 minute intervals through out the
day. The data consists of two months of data from an anonymous
individual collected during the months of October and November, 2012
and include the number of steps taken in 5 minute intervals each day.

The main topics investigated are

1.   What is mean total number of steps taken per day?
2.  What is the average daily activity pattern?
3.  Imputing missing values
4.  Are there differences in activity patterns between weekdays and weekends?

More details are given below.

## Loading and preprocessing the data
Set the working directory and read in the file holding the data.

```r
setwd("~/dataScienceAssets/05_ReproducibleResearch/assignment1/RepData_PeerAssessment1")
df <- read.csv("activity.csv")
```
No preprocessing is needed: although there are NA these will be dealt with later.

## What is mean total number of steps taken per day?
We use aggregate to tot up the steps by day, then draw a histogram of the result.
We arbitrarily choose to have 12 bins.


```r
tot<-aggregate(steps~date,data=df,sum)
hist(tot$steps,breaks=12)
```

![](./PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

To find the mean and median of the totals, we use summary.

```r
summary(tot)
```

```
##          date        steps      
##  2012-10-02: 1   Min.   :   41  
##  2012-10-03: 1   1st Qu.: 8841  
##  2012-10-04: 1   Median :10765  
##  2012-10-05: 1   Mean   :10766  
##  2012-10-06: 1   3rd Qu.:13294  
##  2012-10-07: 1   Max.   :21194  
##  (Other)   :47
```

## What is the average daily activity pattern?
We average over days the number of steps taken for each 5-minute interval, making the results into a time series and plotting it. We identify which interval has the maximum; it turns out to be the one starting at 0835.


```r
ave<-aggregate(steps~interval,data=df,mean)
tsa<-ts(ave$steps)
plot.ts(tsa)
```

![](./PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

```r
max(tsa)
```

```
## [1] 206.1698
```

```r
which.max(tsa)
```

```
## [1] 104
```

```r
df$interval[104]
```

```
## [1] 835
```


## Imputing missing values
There are some NA values; we use the simplest imputation scheme we can think of, to replace them all with the overall mean of steps. We duplicate df to begin with just in case we want it later. We make a histogram and compare the mean and median. The changes due to imputation are trivial.



```r
msteps<-mean(df$steps,na.rm=TRUE)
df2<-df
df2$steps[is.na(df2$steps)]<-msteps
tot2<-aggregate(steps~date,data=df2,sum)
hist(tot2$steps,breaks=12)
```

![](./PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

```r
summary(tot2)
```

```
##          date        steps      
##  2012-10-01: 1   Min.   :   41  
##  2012-10-02: 1   1st Qu.: 9819  
##  2012-10-03: 1   Median :10766  
##  2012-10-04: 1   Mean   :10766  
##  2012-10-05: 1   3rd Qu.:12811  
##  2012-10-06: 1   Max.   :21194  
##  (Other)   :55
```

```r
summary(tot)
```

```
##          date        steps      
##  2012-10-02: 1   Min.   :   41  
##  2012-10-03: 1   1st Qu.: 8841  
##  2012-10-04: 1   Median :10765  
##  2012-10-05: 1   Mean   :10766  
##  2012-10-06: 1   3rd Qu.:13294  
##  2012-10-07: 1   Max.   :21194  
##  (Other)   :47
```
## Are there differences in activity patterns between weekdays and weekends?
To create a column with a weekday/weekend factor, we first determine what day the data start, using the weekdays() function - it's a monday - , then using rep we create 1440 weekday entries followed by 576 weekend entries, rpeated to fill a column length 17568.
We add the column to the dataframe, then subset it to two dataframes, dfd and dfe for weekdays and weekends. Then we repeat what we did to plot the times series for al days, using par to enable a panel display. We see at a glance that the pattern is very differnt on weekdays with a single main peak at getting up time on weekdays, but much more up and dowmn on weekends.


```r
weekdays(as.Date("2012-10-01"))
```

```
## [1] "Monday"
```

```r
weekwhat<-rep(c(rep("weekday",1440),rep("weekend",576)),length.out=17568)
df3<-data.frame(df2,weekwhat)
dfd<-df3[df3$weekwhat=="weekday",]
dfe<-df3[df3$weekwhat=="weekend",]
aved<-aggregate(steps~interval,data=dfd,mean)
tsad<-ts(aved$steps)
avee<-aggregate(steps~interval,data=dfe,mean)
tsae<-ts(avee$steps)
par(mfrow=c(2,1))
plot(tsad)
plot(tsae)
```

![](./PA1_template_files/figure-html/unnamed-chunk-6-1.png) 
