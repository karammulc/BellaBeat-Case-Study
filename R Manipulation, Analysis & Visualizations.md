---
title: "Bellabeat data analysis"
output: pdf_document
date: "2024-04-18"
---

This markdown document presents an analysis for the Bellabeats dataset. This dataset contains tables containing information about health data. CSVs contain information including daily activity, calories, intensities, steps and sleep.

#### Installing and loading required libraries

```{r}
install.packages(c("tidyverse", "lubridate", "ggplot2"))
```


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

#### Continued Dataset Exploration and Manipulation
#### General Exploration
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

#### Ensuring date and time columns in the datasets are converted from chr to POSIXct format
```{r}
# Convert ActivityDay to Date format
dailyactivity$ActivityDay <- as.Date(dailyactivity$ActivityDay, format = "%Y-%m-%d")
dailycalories$ActivityDay <- as.Date(dailycalories$ActivityDay, format = "%Y-%m-%d")
dailyintensities$ActivityDay <- as.Date(dailyintensities$ActivityDay, format = "%Y-%m-%d")
dailystep$ActivityDay <- as.Date(dailystep$ActivityDay, format = "%Y-%m-%d")
```

#### Converting ActivityDay to POSIXct format for sleepday
```{r}
sleepday$ActivityDay <- as.POSIXct(sleepday$ActivityDay, format = "%Y-%m-%d %H:%M:%S")
```

#### Converting ActivityHour to POSIXct format
```{r}
hourlyintensities$ActivityHour <- as.POSIXct(hourlyintensities$ActivityHour, format = "%Y-%m-%d %H:%M:%S")
hourlysteps$ActivityHour <- as.POSIXct(hourlysteps$ActivityHour, format = "%Y-%m-%d %H:%M:%S")
```

------------------------------------------------------------------------
### Summary Statistics

#### Total Steps Summary Statistic Variable Assignment
```{r}
steps_summary <- dailyactivity %>% 
  summarize(
    min_steps = min(TotalSteps),
    max_steps = max(TotalSteps),
    mean_steps = mean(TotalSteps),
    median_steps = median(TotalSteps)
  )
```

#### Total Distance Summary Statistic Variable Assignment
```{r}
distance_summary <- dailyactivity %>%
  summarize(
    min_distance = min(TotalDistance),
    max_distance = max(TotalDistance),
    mean_distance = mean(TotalDistance),
    median_distance = median(TotalDistance)
  ) 
```

#### Calories Summary Statistic Variable Assignment
```{r}
calories_summary <- dailyactivity %>% 
  summary(
    min_calories = min(Calories),
    max_calories = max(Calories),
    mean_calories = mean(Calories),
    median_distance = median(Calories)
  )
```


#### Averages by Week
```{r}
dailyactivity_weekly <- dailyactivity %>%
  mutate(Week = floor_date(ActivityDay, unit = "week")) %>%
  group_by(Week) %>%
  summarize(AvgDistance = mean(TotalDistance))
```


#### Printing Summary Statisitics
```{r}
print(steps_summary)
print(distance_summary)
print(calories_summary)
```


------------------------------------------------------------------------

#### Setting orders for visualizations

```{r}
day_of_week_order <- c("Monday", "Tuesday", "Wednesday","Thursday","Friday","Saturday","Sunday")
```


```{r}
day_period_order <- c("Morning", "Afternoon", "Night")
```

------------------------------------------------------------------------

##### Creating DayType

```{r}
dailyactivity$DayType <- ifelse(dailyactivity$DayOfWeek %in% c("Saturday", "Sunday"), "Weekend", "Weekday")
```

##### Merging sleepday and dailyactivity tables

```{r}
merged_sleepday_dailyactivity <- merge(sleepday, dailyactivity, by = c("Id", "ActivityDay","DayOfWeek"))

```

##### Calculating the average steps, calories burned, and distance traveled for weekdays and weekends separately

```{r}
activity_summary_DayType<- dailyactivity  %>%
    group_by(DayType)  %>%
    summarize(AvgSteps = mean(TotalSteps),
    AvgCalories = mean(Calories),
    AvgDistance = mean(TotalDistance))
```


```{r}
print(activity_summary_DayType)
```


#### Creating average steps per day period variable

```{r}
hourlysteps_dayperiod <- hourlysteps %>%
  group_by(DayPeriod) %>%
  summarize(avgsteps = mean(StepTotal))
```

```{r}
print(hourlysteps_dayperiod)
```


##### Calculating average total minutes asleep for each day type

```{r}
merged_sleepday_dailyactivity_avg <- merged_sleepday_dailyactivity %>%
  group_by(DayType) %>%
  summarise(AvgTotalMinutesAsleep = mean(TotalMinutesAsleep))
```

------------------------------------------------------------------------

##### Aggregation of Average Activity by User

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

------------------------------------------------------------------------

#### Weekly Average Distance Traveled Over Time

```{r}
plot1 <-
ggplot(dailyactivity_weekly, aes(x = Week, y = AvgDistance)) +
  geom_line() +
  labs(title = "Weekly Average Distance Traveled Over Time", x = "Week", y = "Average Distance") +
   theme(axis.title = element_text(size = 13),
        axis.text = element_text(size = 12),
        plot.title = element_text(size = 14, face = "bold"))
```


------------------------------------------------------------------------

#### Average Daily Steps by Day of the Week 

```{r}
plot2<-
ggplot(dailyactivity, aes(x = factor(DayOfWeek, levels = day_of_week_order), y = TotalSteps, fill = DayOfWeek)) +
  geom_bar(stat = "summary" , fun = "mean") +
  labs(title = "Average Daily Steps by Day of the Week", x = "Day of the Week", y = "Average Steps") +
  scale_fill_manual(values = c("Monday" = "steelblue",
                               "Tuesday" = "darkorange",
                               "Wednesday" = "forestgreen",
                               "Thursday" = "#CD8783",
                               "Friday" = "gold",
                               "Saturday" = "firebrick",
                               "Sunday" = "darkturquoise")) +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
        axis.title = element_text(size = 13),
        axis.text = element_text(size = 12),
        plot.title = element_text(size = 14, face = "bold"))
```

#### Average Calories by Day of Week

```{r}
plot3 <-
ggplot(dailyactivity, aes(x = factor(DayOfWeek, levels = day_of_week_order), y = Calories, fill = DayOfWeek)) +
  geom_bar(stat = "summary", fun = "mean") +
  labs(title = "Average Daily Calories Burned by Day of the Week", x = "Day of the Week", y = "Average Calories") + 
   scale_fill_manual(values = c("Monday" = "steelblue",
                               "Tuesday" = "darkorange",
                               "Wednesday" = "forestgreen",
                               "Thursday" = "#CD8783",
                               "Friday" = "gold",
                               "Saturday" = "firebrick",
                               "Sunday" = "darkturquoise")) +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
        axis.title = element_text(size = 13),
        axis.text = element_text(size = 12),
        plot.title = element_text(size = 14, face = "bold"))
```

#### Average Distance by Day of Week 

```{r}
plot4 <-
ggplot(dailyactivity, aes(x = factor(DayOfWeek, levels = day_of_week_order), y = TotalDistance, fill = DayOfWeek)) +
  geom_bar(stat = "summary", fun = "mean") +
  labs(title = "Average Distance by Day of Week", x = "Day of the Week", y = "Total Distance") +
 scale_fill_manual(values = c("Monday" = "steelblue",
                               "Tuesday" = "darkorange",
                               "Wednesday" = "forestgreen",
                               "Thursday" = "#CD8783",
                               "Friday" = "gold",
                               "Saturday" = "firebrick",
                               "Sunday" = "darkturquoise")) +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
        axis.title = element_text(size = 13),
        axis.text = element_text(size = 12),
        plot.title = element_text(size = 14, face = "bold"))
```

#### Average daily distance traveled by day of the week

```{r}
plot5 <-
ggplot(dailyactivity, aes(x = factor(DayOfWeek, levels = day_of_week_order), y = TotalDistance,fill = DayOfWeek)) +
  geom_bar(stat = "summary", fun = "mean") +
  labs(title = "Average Daily Distance Traveled by Day of the Week", x = "Day of the Week", y = "Average Distance") +
  scale_fill_manual(values = c("Monday" = "steelblue",
                               "Tuesday" = "darkorange",
                               "Wednesday" = "forestgreen",
                               "Thursday" = "#CD8783",
                               "Friday" = "gold",
                               "Saturday" = "firebrick",
                               "Sunday" = "darkturquoise")) +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
         axis.title = element_text(size = 13),
        axis.text = element_text(size = 12),
        plot.title = element_text(size = 14, face = "bold"))
```


------------------------------------------------------------------------

#### Average Steps by Time of Day

```{r}
plot6 <-
ggplot(hourlysteps_dayperiod, aes(x = factor(DayPeriod, levels = day_period_order), y = avgsteps, fill = DayPeriod)) +
  geom_bar(stat = "identity") +
  geom_text(aes(label = round(avgsteps)), vjust = -0.5, size = 3) +
  labs(title = "Average Steps by Time of Day", x = "Time of Day", y = "Average Steps") +
  scale_fill_manual(values = c("Morning" = "skyblue", "Afternoon" = "#CD8783", "Night" = "gold")) +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
       axis.title = element_text(size = 13),
        axis.text = element_text(size = 12),
        plot.title = element_text(size = 14, face = "bold"))
```

### Average Intensity by Time of Day

```{r}
plot7 <-
ggplot(hourlyintensities, aes(x = factor(DayPeriod, levels = day_period_order), y = AverageIntensity, fill = DayPeriod)) +
  geom_bar(stat = "summary", fun = "mean") +
  geom_text(stat = "summary", fun = "mean", aes(label = round(..y.., 2)), vjust = -0.5, size = 3) +
  labs(title = "Average Intensity by Time of Day", x = "Time of Day", y = "Average Intensity") +
  scale_fill_manual(values = c("Morning" = "skyblue", "Afternoon" = "#CD8783", "Night" = "gold")) +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
         axis.title = element_text(size = 13),
        axis.text = element_text(size = 12),
        plot.title = element_text(size = 14, face = "bold"))
```

#### Average Burned Calories by Weekday vs. Weekend

```{r}
plot8 <-
ggplot(activity_summary_DayType, aes(x = DayType, y = AvgCalories, fill = DayType)) +
  geom_bar(stat = "identity", width = .75) +
  geom_text(aes(label = round(AvgCalories)), vjust = -0.5, size = 3) +
  scale_fill_manual(values = c("Weekday" = "#CD8783", "Weekend" = "skyblue")) +
  labs(title = "Average Calories by Weekday vs. Weekend",
       x = "Day Type",
       y = "Average Calories per Day") +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
        axis.title = element_text(size = 13),
        axis.text = element_text(size = 12),
        plot.title = element_text(size = 14, face = "bold"))
```

#### Average Steps by Weekday vs. Weekend

```{r}
plot9 <-
ggplot(activity_summary_DayType, aes(x = DayType, y = AvgSteps, fill = DayType)) +
  geom_bar(stat = "identity", width = .75) +
  geom_text(aes(label = round(AvgSteps)), vjust = -0.5, size = 3) +
  scale_fill_manual(values = c("Weekday" = "#CD8783", "Weekend" = "skyblue")) +
  labs(title = "Average Steps by Weekday vs. Weekend",x = "Day Type", y = "Average Steps per Day") +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
        axis.title = element_text(size = 12),
        axis.text = element_text(size = 10),
        plot.title = element_text(size = 14, face = "bold"))
```

#### Average Total Minutes Asleep by Weekday vs. Weekend

```{r}
plot10 <- 
ggplot(merged_sleepday_dailyactivity_avg, aes(x = DayType, y = AvgTotalMinutesAsleep, fill = DayType)) +
  geom_bar(stat = "identity", width = .75) +
  geom_text(aes(label = round(AvgTotalMinutesAsleep)), vjust = -0.5, size = 3) +
  scale_fill_manual(values = c("Weekday" = "#CD8783", "Weekend" = "skyblue")) +
  labs(title = "Average Total Minutes Asleep by Weekday vs. Weekend", x = "Day Type", y = "Average Minutes Asleep per Day") +
  theme_minimal() +
  theme(legend.position = "top",
        legend.title = element_blank(),
        axis.title = element_text(size = 13),
        axis.text = element_text(size = 12),
        plot.title = element_text(size = 14, face = "bold"))
```

#### Total Distance vs. Total Minutes Asleep

```{r}
plot11 <- 
ggplot(data = merged_sleepday_dailyactivity, mapping = aes(x = TotalDistance, y = TotalMinutesAsleep, color = DayType)) +
  geom_point() +
  geom_smooth() +
  labs(title = "Relationship between Total Distance and Total Minutes Asleep",
       subtitle = "Comparing Weekdays and Weekends", x = "Total Distance", y = "Total Minutes Asleep") +
  scale_color_discrete(name = "Day Type", labels = c("Weekday", "Weekend"))
```

#### Relationship between Sedentary Minutes and Total Time In Bed

```{r}
plot12 <-
ggplot(data = merged_sleepday_dailyactivity, mapping = aes(x = TotalTimeInBed, y = SedentaryMinutes, color = DayType)) +
  geom_point() +
  geom_smooth() +
  labs(title = "Relationship between Sedentary Minutes and Total Time In Bed",
       subtitle = "Comparing Weekdays and Weekends", x = "Total Time in Bed", y = "SedentaryMinutes") +
  scale_color_discrete(name = "Day Type", labels = c("Weekday", "Weekend"))
```

#### Relationship between Total Steps and Calories Burned

```{r}
plot13 <-
ggplot(data = merged_sleepday_dailyactivity, mapping = aes(x = TotalSteps, y = Calories, color = DayType)) +
  geom_point() +
  geom_smooth() +
  labs(title = "Relationship between Total Steps and Calories Burned",
       subtitle = "Comparing Weekdays and Weekends", x = "Total Steps", y = "Calories Burned") +
  scale_color_discrete(name = "Day Type", labels = c("Weekday", "Weekend"))
```

#### Relationship between Very Active Minutes and Total Minutes Asleep

```{r}
plot14 <-
ggplot(data = merged_sleepday_dailyactivity, mapping = aes(x = VeryActiveMinutes, y = TotalMinutesAsleep, color = DayType)) +
  geom_point() +
  geom_smooth() +
  labs(title = "Relationship between Very Active Minutes and Total Minutes Asleep",
       subtitle = "Comparing Weekdays and Weekends", x = "Very Active Minutes", y = "Total Minutes Asleep") +
  scale_color_discrete(name = "Day Type", labels = c("Weekday", "Weekend")) 

```

#### Total Distance vs. Total Minutes Asleep

```{r}
plot15<- 
ggplot(data = merged_sleepday_dailyactivity, mapping = aes(x =  VeryActiveMinutes, y = Calories, color = DayType)) +
  geom_point() +
  geom_smooth() + 
  labs(title = "Relationship between Very Active Minutes and Calories Burned",
       subtitle = "Comparing Weekdays and Weekends", x = " VeryActiveMinutes", y = "Calories") +
  scale_color_discrete(name = "Day Type", labels = c("Weekday", "Weekend"))
```

------------------------------------------------------------------------

#### Creating a loop to save each plot
```{r}
# Create a vector of plot names
plot_names <- paste0("plot", 1:15)


for (i in 1:length(plot_names)) {
  # Get the plot object based on the plot name
  plot <- get(plot_names[i])
  

  plot <- plot + 
    theme(plot.margin = margin(t = 20, r = 20, b = 20, l = 20, unit = "pt"))

  filename <- paste0("plot_", i, ".png")

  ggsave(filename, plot, width = 8, height = 6, dpi = 300)
}

```

        axis.text = element_text(size = 10),
        plot.title = element_text(size = 14, face = "bold"))
```

