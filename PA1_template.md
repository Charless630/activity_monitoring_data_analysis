## Reporducible Research: Peer-graded Assignment 1

### Installing & Loading the necessary packages

library(ggplot2)
library(scales)
library(Hmisc)


### Loading & preparing the data for analysis
##### 1. Load the data

activity_data <- read.csv("activity.csv")

##### 2. Process and transform the data (if necessary) into a suitable format for data analysis

activity_data$interval <- as.POSIXct(activity_data$interval)

### What is mean total number of steps taken per day?

steps_per_day <- tapply(activity_data$steps, activity_data$date, sum, na.rm = TRUE)


##### 1. Make a histogram of the total number of steps taken each day

qplot(steps_per_day, xlab = "Total Steps Per Day", ylab = "Frequency", binwidth = 500)

##### 2. Calculate and report the mean and median of the total number of steps taken per day

steps_per_day_mean <- mean(steps_per_day)

steps_per_day_median <- median(steps_per_day)

### What is the average daily activity pattern?

avg_steps_timeb <- avg_steps_timeb <- aggregate(steps ~ interval, data=activity_data, FUN=mean, na.rm=TRUE)

##### 1. Make a time series plot (i.e.type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

activity_data$time_interval <- format(activity_data$interval, format="%H:%M")

avg_steps_by_interval <- aggregate(steps ~ time_interval, data=activity_data, FUN=mean, na.rm=TRUE)

avg_steps_by_interval <- avg_steps_by_interval[order(strptime(avg_steps_by_interval$time_interval, "%H:%M")),]

avg_steps_by_interval$time_interval <- as.POSIXct(strptime(avg_steps_by_interval$time_interval, "%H:%M"))

ggplot(avg_steps_by_interval, aes(x=time_interval, y=steps)) +
  geom_line() +
  scale_x_datetime(date_labels="%H:%M", date_breaks="2 hours") + # Display labels every 2 hours
  labs(title="Average Steps by 5-minute Interval",
       x="5-minute interval",
       y="Average number of steps") +
  theme_minimal()


##### 1.Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

max_interval <- avg_steps_by_interval[which.max(avg_steps_by_interval$steps), "time_interval"]

cat("The 5-minute interval with the maximum average steps is:", max_interval, "\n")

### Imputing missing values

##### 1. Calculate and report the total number of missing values in the dataset

total_missing_values <- sum(is.na(activity_data))
cat("The total number of missing values in the dataset is:", total_missing_values, "\n")

rows_with_na <- sum(apply(is.na(activity_data), 1, any))
cat("The total number of rows with NAs in the dataset is:", rows_with_na, "\n")


##### 2. Devise a strategy for filling in all of the missing values in the dataset

activity_data$time_interval <- format(activity_data$interval, format="%H:%M")

mean_steps_by_interval <- aggregate(steps ~ time_interval, data=activity_data, FUN=mean, na.rm=TRUE)

activity_data <- merge(activity_data, mean_steps_by_interval, by="time_interval", suffixes=c("", "_mean"))
activity_data$steps[is.na(activity_data$steps)] <- activity_data$steps_mean[is.na(activity_data$steps)]


##### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

new_activity_data <- activity_data

new_activity_data$time_interval <- format(new_activity_data$interval, format="%H:%M")

new_activity_data$steps <- with(new_activity_data, ave(steps, time_interval, FUN=function(x) ifelse(is.na(x), mean(x, na.rm=TRUE), x)))

##### 4/a. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day

total_steps_per_day <- aggregate(steps ~ date, new_activity_data, sum)

ggplot(total_steps_per_day, aes(x=steps)) +
  geom_histogram(binwidth=1000, fill="blue", alpha=0.7) +
  labs(title="Total Number of Steps Taken Each Day",
       x="Total steps",
       y="Frequency") +
  theme_minimal()

##### 4/b. Calculate and report the mean and median total number of steps taken per day

mean_steps_per_day <- mean(total_steps_per_day$steps)
median_steps_per_day <- median(total_steps_per_day$steps)

cat("Mean total number of steps taken per day:", format(mean_steps_per_day, big.mark="."), "\n")
cat("Median total number of steps taken per day:", format(median_steps_per_day, big.mark="."), "\n")

### Are there differences in activity patterns between weekdays and weekends?

new_activity_data$date <- as.Date(new_activity_data$date)

new_activity_data$day_type <- ifelse(weekdays(new_activity_data$date) %in% c("Saturday", "Sunday"), "Weekend", "Weekday")

summary_stats <- aggregate(steps ~ day_type, new_activity_data, function(x) c(mean = mean(x), median = median(x)))
print(summary_stats)

# Average steps for each 5-minute interval by day type
avg_steps_by_interval <- aggregate(steps ~ interval + day_type, new_activity_data, mean)

# Plotting
ggplot(avg_steps_by_interval, aes(x=interval, y=steps, color=day_type)) +
  geom_line() +
  labs(title="Average Steps by 5-Minute Interval",
       x="Time Interval",
       y="Average Steps") +
  theme_minimal() +
  scale_x_datetime(date_breaks="2 hours", date_labels="%H:%M") # Adjust breaks and labels as needed

##### 1.Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day

new_activity_data$day_type <- as.factor(new_activity_data$day_type)

##### 2.Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis)

ggplot(avg_steps_by_interval, aes(x=interval, y=steps)) +
  geom_line() +
  facet_grid(day_type ~ .) + # Separates the plot by day_type
  labs(title="Average Steps by 5-Minute Interval",
       x="Time Interval",
       y="Average Steps",
       caption="Weekdays and weekends are separated into two panels.") +
  theme_minimal() +
  scale_x_datetime(date_breaks="2 hours", date_labels="%H:%M")