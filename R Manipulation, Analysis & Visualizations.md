---
title: "Bellabeat data analysis"
output: pdf_document
date: "2024-04-18"
---

This markdown document presents an analysis for the Bellabeats dataset. This dataset contains tables containing information about health data. CSVs contain information including daily activity, calories, intensities, steps and sleep.

#### Loading required libraries

```{r}
library(tidyverse)
library(lubridate)
library(ggplot2)
```


#### Loading data set
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

### General Exploration
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

### Ensuring date and time columns in the datasets are converted from chr to POSIXct format

```{r}
# Convert ActivityDay to Date format
dailyactivity$ActivityDay <- as.Date(dailyactivity$ActivityDay, format = "%Y-%m-%d")
dailycalories$ActivityDay <- as.Date(dailycalories$ActivityDay, format = "%Y-%m-%d")
dailyintensities$ActivityDay <- as.Date(dailyintensities$ActivityDay, format = "%Y-%m-%d")
dailystep$ActivityDay <- as.Date(dailystep$ActivityDay, format = "%Y-%m-%d")
sleepday$ActivityDay <- as.Date(sleepday$ActivityDay, format = "%m/%d/%Y")
```

### Converting ActivityHour to POSIXct format
```{r}
hourlyintensities$ActivityHour <- as.POSIXct(hourlyintensities$ActivityHour, format = "%Y-%m-%d %H:%M:%S")
hourlysteps$ActivityHour <- as.POSIXct(hourlysteps$ActivityHour, format = "%Y-%m-%d %H:%M:%S")
```


### Setting order for visualizations
```{r}
day_of_week_order <- c("Monday", "Tuesday", "Wednesday","Thursday","Friday","Saturday","Sunday")
```


### Aggregating data by week
```{r}
dailyactivity_weekly <- dailyactivity %>%
  mutate(Week = floor_date(ActivityDay, unit = "week")) %>%
  group_by(Week) %>%
  summarize(AvgDistance = mean(TotalDistance))
```


### Aggregating total steps by day of the week

```{r}
steps_by_day <- aggregate(TotalSteps ~ DayOfWeek, data = dailyactivity, FUN = sum)
```

### Creating average steps per day period variable

```{r}
hourlysteps_dayperiod <- hourlysteps %>%
  group_by(DayPeriod) %>%
  summarize(avgsteps = mean(StepTotal))
```

### Specifying the desired order of day periods

```{r}
day_period_order <- c("Morning", "Afternoon", "Night")
```

### Creating Daytype

```{r}
dailyactivity$DayType <- ifelse(dailyactivity$DayOfWeek %in% c("Saturday", "Sunday"), "Weekend", "Weekday")
```


###  Merging sleepday and dailyactivity datasets

```{r}
merged_sleepday_dailyactivity <- merge(sleepday, dailyactivity, by = c("Id", "ActivityDay","DayOfWeek"))
```

------------------------------------------------------------------------
### User Summary

```{r}
user_activity <- dailyactivity %>%
  group_by(Id) %>%
  summarize(AvgDailySteps = mean(TotalSteps),
            AvgDailyCalories = mean(Calories),
            AvgDailyActiveMinutes = mean(VeryActiveMinutes + FairlyActiveMinutes + LightlyActiveMinutes))
```


```{r}
print(user_activity)
```

---------------------------------------
### Daily Activity Table Exploration

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

### *Weekly Average Distance Traveled Over Time - Line Chart*

```{r}
ggplot(dailyactivity_weekly, aes(x = Week, y = AvgDistance)) +
  geom_line() +
  labs(title = "Weekly Average Distance Traveled Over Time", x = "Week", y = "Average Distance")
```

### *Average Daily Steps by Day of the Week - Bar Chart*

```{r}
ggplot(dailyactivity, aes(x = factor(DayOfWeek, levels = day_of_week_order), y = TotalSteps, fill = DayOfWeek)) +
  geom_bar(stat = "summary" , fun = "mean") +
  labs(title = "Average Daily Steps by Day of the Week", x = "Day of the Week", y = "Average Steps") +
  scale_fill_manual(values = c("Monday" = "steelblue",
                               "Tuesday" = "darkorange",
                               "Wednesday" = "forestgreen",
                               "Thursday" = "orchid",
                               "Friday" = "gold",
                               "Saturday" = "firebrick",
                               "Sunday" = "darkturquoise")) +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
        axis.title = element_text(size = 12),
        axis.text = element_text(size = 10),
        plot.title = element_text(size = 14, face = "bold"))
```

### *Average Calories by Day of Week - Bar Chart*

```{r}
ggplot(dailyactivity, aes(x = factor(DayOfWeek, levels = day_of_week_order), y = Calories, fill = DayOfWeek)) +
  geom_bar(stat = "summary", fun = "mean") +
  labs(title = "Average Daily Calories Burned by Day of the Week", x = "Day of the Week", y = "Average Calories") + 
   scale_fill_manual(values = c("Monday" = "steelblue",
                               "Tuesday" = "darkorange",
                               "Wednesday" = "forestgreen",
                               "Thursday" = "orchid",
                               "Friday" = "gold",
                               "Saturday" = "firebrick",
                               "Sunday" = "darkturquoise")) +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
        axis.title = element_text(size = 12),
        axis.text = element_text(size = 10),
        plot.title = element_text(size = 14, face = "bold"))
```

### *Average Distance by Day of Week - Bar Chart*

```{r}
ggplot(dailyactivity, aes(x = factor(DayOfWeek, levels = day_of_week_order), y = TotalDistance, fill = DayOfWeek)) +
  geom_bar(stat = "summary", fun = "mean") +
  labs(title = "Average Distance by Day of Week", x = "Day of the Week", y = "Total Distance") +
 scale_fill_manual(values = c("Monday" = "steelblue",
                               "Tuesday" = "darkorange",
                               "Wednesday" = "forestgreen",
                               "Thursday" = "orchid",
                               "Friday" = "gold",
                               "Saturday" = "firebrick",
                               "Sunday" = "darkturquoise")) +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
        axis.title = element_text(size = 12),
        axis.text = element_text(size = 10),
        plot.title = element_text(size = 14, face = "bold"))
```

### *Average daily distance traveled by day of the week - Bar Chart*

```{r}
ggplot(dailyactivity, aes(x = factor(DayOfWeek, levels = day_of_week_order), y = TotalDistance,fill = DayOfWeek)) +
  geom_bar(stat = "summary", fun = "mean") +
  labs(title = "Average Daily Distance Traveled by Day of the Week", x = "Day of the Week", y = "Average Distance") +
  scale_fill_manual(values = c("Monday" = "steelblue",
                               "Tuesday" = "darkorange",
                               "Wednesday" = "forestgreen",
                               "Thursday" = "orchid",
                               "Friday" = "gold",
                               "Saturday" = "firebrick",
                               "Sunday" = "darkturquoise")) +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
        axis.title = element_text(size = 12),
        axis.text = element_text(size = 10),
        plot.title = element_text(size = 14, face = "bold"))
```

### *Weekly Average Distance Traveled Over Time - Line Chart*

```{r}
# Line plot for weekly average distance traveled
ggplot(dailyactivity_weekly, aes(x = Week, y = AvgDistance)) +
  geom_line() +
  labs(title = "Weekly Average Distance Traveled Over Time", x = "Week", y = "Average Distance")

```

------------------------------------------------------------------------

# Hourly Tables Exploration

```{r}
str(hourlyintensities)
str(hourlysteps)
```

```{r}
summary(hourlyintensities)
summary(hourlysteps)
```

### Average Steps by Time of Day

```{r}
ggplot(hourlysteps_dayperiod, aes(x = factor(DayPeriod, levels = day_period_order), y = avgsteps, fill = DayPeriod)) +
  geom_bar(stat = "identity") +
  labs(title = "Average Steps by Time of Day", x = "Time of Day", y = "Average Steps") +
  scale_fill_manual(values = c("Morning" = "skyblue", "Afternoon" = "orchid", "Night" = "gold")) +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
        axis.title = element_text(size = 12),
        axis.text = element_text(size = 10),
        plot.title = element_text(size = 14, face = "bold"))
```

### Average Intensity by Time of Day

```{r}
ggplot(hourlyintensities, aes(x = factor(DayPeriod, levels = day_period_order), y = AverageIntensity, fill = DayPeriod)) +
  geom_bar(stat = "summary", fun = "mean") +
  labs(title = "Average Intensity by Time of Day", x = "Time of Day", y = "Average Intensity") +
  scale_fill_manual(values = c("Morning" = "skyblue", "Afternoon" = "orchid", "Night" = "gold")) +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
        axis.title = element_text(size = 12),
        axis.text = element_text(size = 10),
        plot.title = element_text(size = 14, face = "bold"))
```

## *Analyzing Activity levels by Weekday or Weekend*

```{r}
dailyactivity$DayType <- ifelse(dailyactivity$DayOfWeek %in% c("Saturday", "Sunday"), "Weekend", "Weekday")
```

Calculating the average steps, calories burned, and distance traveled for weekdays and weekends separately

```{r}
activity_summary <- dailyactivity  %>%
    group_by(DayType)  %>%
    summarize(AvgSteps = mean(TotalSteps),
    AvgCalories = mean(Calories),
    AvgDistance = mean(TotalDistance))
```

```{r}
print(activity_summary)
```

```{r}
ggplot(activity_summary, aes(x = DayType, y = AvgCalories, fill = DayType)) +
  geom_bar(stat = "identity", width = .75) +
  geom_text(aes(label = round(AvgCalories)), vjust = -0.5, size = 3) +
  scale_fill_manual(values = c("Weekday" = "steelblue", "Weekend" = "forestgreen")) +
  labs(title = "Average Calories by Weekday vs. Weekend",
       x = "Day Type",
       y = "Average Calories per Day") +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
        axis.title = element_text(size = 12),
        axis.text = element_text(size = 10),
        plot.title = element_text(size = 14, face = "bold"))
```

### *Average Steps by Weekday vs. Weekend - Bar Chart*

```{r}
ggplot(activity_summary, aes(x = DayType, y = AvgSteps, fill = DayType)) +
  geom_bar(stat = "identity", width = .75) +
  geom_text(aes(label = round(AvgSteps)), vjust = -0.5, size = 3) +
  scale_fill_manual(values = c("Weekday" = "steelblue", "Weekend" = "forestgreen")) +
  labs(title = "Average Steps by Weekday vs. Weekend",
       x = "Day Type",
       y = "Average Steps per Day") +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
        axis.title = element_text(size = 12),
        axis.text = element_text(size = 10),
        plot.title = element_text(size = 14, face = "bold"))
```

```{r}
ggplot(data = dailyactivity, mapping = aes(x = TotalSteps, y = Calories, color = Id)) +
  geom_point() +
  geom_smooth() +
  labs(title = "Total Steps vs. Calories")
```

------------------------------------------------------------------------

# Exploring Merged sleepday and dailyactivity tables

```{r}
str(merged_sleepday_dailyactivity)
```

```{r}
ggplot(data = merged_sleepday_dailyactivity, mapping = aes(x = TotalDistance, y = TotalMinutesAsleep,color = Id)) +
  geom_point() +
  geom_smooth() +
  labs(title = "Very Active Minutes vs. Total Minutes Asleep")
```


```{r}
t.test(TotalDistance ~ TotalMinutesAsleep, data = merged_sleepday_dailyactivity)
```



```{r}
ggplot(data = dailyactivity, mapping = aes(x = TotalDistance, y = Calories,color = Id)) +
  geom_point() +
  geom_smooth() +
  labs(title = "Total Distance vs. Calories Burned")
```




```{r}
print(user_activity)
```


```{r}

user_activity <- dailyactivity %>%
  group_by(Id) %>%
  summarize(AvgDailySteps = mean(TotalSteps),
            AvgDailyCalories = mean(Calories),
            AvgDailyActiveMinutes = mean(VeryActiveMinutes + FairlyActiveMinutes + LightlyActiveMinutes))
```



```{r}
ggplot(user_activity, aes(x = Id, y = AvgDailyCalories, fill = Id)) +
  geom_bar(stat = "identity") +
  labs(title = "Average Daily Calories Burned by User", x = "User ID", y = "Average Calories") +
  theme_minimal() +
  theme(legend.position = "none",
        axis.title = element_text(size = 12),
        axis.text = element_text(size = 10),
        plot.title = element_text(size = 14, face = "bold"))
```
