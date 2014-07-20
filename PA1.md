# Reproducible Research: Peer Assessment 1
==========================================

## 1. Loading and preprocessing the data

```r
unzip("activity.zip")
data <- read.csv("activity.csv",as.is=T)
```

## 2. What is mean/total number of steps taken per day?


```r
Steps <- tapply(data[,1],data[,2],sum,na.rm=T)
hist(Steps,breaks=12,main="Histogram of the total number of steps taken each day")
```

![plot of chunk Q2.1](figure/Q2.1.png) 

The mean and median total number of steps taken per day is:


```r
mean(Steps)
```

```
## [1] 9354
```

```r
median(Steps)
```

```
## [1] 10395
```

## 3. What is the average daily activity pattern?


```r
Average5M <- tapply(data[,1],data[,3],mean,na.rm=T)
Minutes <- names(Average5M)
plot(Minutes,Average5M,type="l",ylab="Average number of steps taken")
title("Average daily activity pattern")
```

![plot of chunk Q3.1](figure/Q3.1.png) 

The 5-minute interval containing the maximum number of steps is:


```r
Minutes[which.max(Average5M)]
```

```
## [1] "835"
```


## 4. Imputing missing values

The total number of missing values in the dataset:


```r
sum(is.na(data[,1]))
```

```
## [1] 2304
```

Create a new dataset that is equal to the original dataset but with the missing data filled with the mean for that 5-minute interval:


```r
data2 <- data
for (x in 1:nrow(data2)){
    if(is.na(data2[x,1])){ 
        data2[x,1] <- Average5M[ as.character(data2[x,3]) ] 
    }
}
head(data2)
```

```
##     steps       date interval
## 1 1.71698 2012-10-01        0
## 2 0.33962 2012-10-01        5
## 3 0.13208 2012-10-01       10
## 4 0.15094 2012-10-01       15
## 5 0.07547 2012-10-01       20
## 6 2.09434 2012-10-01       25
```

The histogram of the total number of steps taken each day:


```r
StepsIM <- tapply(data2[,1],data2[,2],sum)
hist(StepsIM,breaks=12,xlab="Steps",main=NULL)
```

![plot of chunk Q4.3](figure/Q4.3.png) 

The mean and median total number of steps taken per day is:


```r
mean(StepsIM)
```

```
## [1] 10766
```

```r
median(StepsIM)
```

```
## [1] 10766
```

From these results, the mean total number of steps taken per day is significantly 
improved, and the median value is comparable to the estimate from the first part 
of the assignment. It also seems that imputing missing data does have significantly influence on the distribution of the total daily number of steps.

## 5. Are there differences in activity patterns between weekdays and weekends?

Preparing data:


```r
# find weekdays and weekend
type <- weekdays(as.Date(data2[,2]),T)

data2weekend <- data2[type == "Sun" | type == "Sat",]
data2weekday <- data2[type != "Sun" & type != "Sat",]

Average5Mweekend <- tapply(data2weekend[,1],data2weekend[,3],mean)
Average5Mweekday <- tapply(data2weekday[,1],data2weekday[,3],mean)
```

Plotting:


```r
plot(Minutes,Average5Mweekday,type="l",col="red",ylab="Average number of steps taken")
lines(Average5Mweekend,type="l",col="blue")
legend("topright",legend=c("weekday","weekend"),col=c("red","blue"),lty=1)
title("Average daily activity pattern")
```

![plot of chunk Q5.2](figure/Q5.2.png) 

As shown in above figure, there's signifcant differences in activity patterns between weekdays and weekends.
