# Activity Analysis Project

## Description

This project analyzes a dataset containing step-counting data to study daily activity patterns. It performs several analyses including:

- Calculating mean total number of steps taken per day.
- Making histograms of total steps taken each day.
- Analyzing average daily activity patterns.
- Handling missing values by imputing the mean.
- Comparing activity patterns between weekdays and weekends.

## Dependencies

The following R packages are required to run the code:

- ggplot2
- scales
- Hmisc

You can install them using the following commands:

```R
install.packages("ggplot2")
install.packages("scales")
install.packages("Hmisc")
```

## How to Run

1. Make sure the working directory is set to the correct path where the `activity.csv` file is located.
2. Run the R code in your preferred R environment.
3. The code will generate plots as PNG files in the working directory.

## Dataset

The dataset `activity.csv` should be present in the working directory. It should contain the following columns:

- `steps`: Number of steps taken in the interval.
- `date`: The date of the observation.
- `interval`: The 5-minute interval in which the observation was made.

## Output

The code generates the following PNG files:

- `plot1.png`: Histogram of total steps per day.
- `avg_num_steps.png`: Time series plot of average steps by 5-minute interval.
- `total_steps.png`: Histogram of total steps per day with missing data filled in.
- `avg_steps.png`: Line plot of average steps by 5-minute interval for weekdays and weekends.
- `weeknd_weekdy.png`: Panel plot for weekdays and weekends.

It also prints to the console:

- Mean and median of total number of steps taken per day.
- 5-minute interval with the maximum average steps.
- Total number of missing values and rows with NAs.
- Mean and median total number of steps taken per day after imputing missing values.
- Summary statistics for weekdays and weekends.

## Note

Make sure the `activity.csv` file is in the correct format and located in the specified directory. Check the date and interval formats in the dataset to match with the code.

## Author

Mario

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.
