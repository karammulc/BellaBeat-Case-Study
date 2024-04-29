---

This markdown document presents an analysis for the Bellabeats dataset. This dataset contains tables containing information about health data. CSVs contain information including daily activity, calories, intensities, steps and sleep.

Loading required libraries
```{r}
library(tidyverse)
library(lubridate)
library(ggplot2)
```

Loading data set
```{r}
dailyactivity <- read.csv("dailyactivityclean.csv")
dailycalories <- read.csv("dailycaloriesclean.csv")
dailyintensities <- read.csv("dailyintensitiesclean.csv")
dailystep <- read.csv("dailystepsclean.csv")
hourlyintensities <- read.csv("hourlyintensitiesclean.csv")
hourlysteps <- read.csv("hourlystepsclean.csv")
sleepday <- read.csv("sleepdayclean.csv")
```

## Continued Dataset Exploration and Manipulation

General Exploration
```{r}
str(dailyactivity)
str(dailycalories)
str(dailyintensities)
str(dailystep)
str(hourlyintensities)
str(hourlysteps)
str(sleepday)
```

```{r}
summary(dailyactivity)
summary(dailycalories)
summary(dailyintensities)
summary(dailystep)
summary(hourlyintensities)
summary(hourlysteps)
summary(sleepday)
```

Ensuring date and time columns in the datasets are converted from chr to POSIXct format
```{r}
# Convert ActivityDay to Date format
dailyactivity$ActivityDay <- as.Date(dailyactivity$ActivityDay, format = "%Y-%m-%d")
dailycalories$ActivityDay <- as.Date(dailycalories$ActivityDay, format = "%Y-%m-%d")
dailyintensities$ActivityDay <- as.Date(dailyintensities$ActivityDay, format = "%Y-%m-%d")
dailystep$ActivityDay <- as.Date(dailystep$ActivityDay, format = "%Y-%m-%d")
```

Converting ActivityDay to POSIXct format for sleepday
```{r}
sleepday$ActivityDay <- as.POSIXct(sleepday$ActivityDay, format = "%Y-%m-%d %H:%M:%S")
```

Converting ActivityHour to POSIXct format
```{r}
hourlyintensities$ActivityHour <- as.POSIXct(hourlyintensities$ActivityHour, format = "%Y-%m-%d %H:%M:%S")
hourlysteps$ActivityHour <- as.POSIXct(hourlysteps$ActivityHour, format = "%Y-%m-%d %H:%M:%S")
```

------------------------------------------------------------------------

## Daily Activity Table Exploration

Total Steps Summary Statistic Variable Assignment
```{r}
steps_summary <- dailyactivity %>% 
  summarize(
    min_steps = min(TotalSteps),
    max_steps = max(TotalSteps),
    mean_steps = mean(TotalSteps),
    median_steps = median(TotalSteps)
  )
```

Total Distance Summary Statistic Variable Assignment
```{r}
distance_summary <- dailyactivity %>%
  summarize(
    min_distance = min(TotalDistance),
    max_distance = max(TotalDistance),
    mean_distance = mean(TotalDistance),
    median_distance = median(TotalDistance)
  ) 
```

Calories Summary Statistic Variable Assignment
```{r}
calories_summary <- dailyactivity %>% 
  summary(
    min_calories = min(Calories),
    max_calories = max(Calories),
    mean_calories = mean(Calories),
    median_distance = median(Calories)
  )
```

Printing Summary Statisitics
```{r}
print(steps_summary)
print(distance_summary)
print(calories_summary)
```

------------------------------------------------------------------------

## Daily Activity by Day of Week Visualizations

### *Line plot for daily steps over time*

```{r}
# Line plot for daily steps over time
ggplot(dailyactivity, aes(x = ActivityDay, y = TotalSteps)) +
  geom_line() +
  labs(title = "Daily Steps Over Time", x = "Date", y = "Total Steps")
```
