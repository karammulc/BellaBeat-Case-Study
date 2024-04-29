# Bellabeat SQL Cleaning & Manipulation

The SQL queries and steps outlined below demonstrate the process of cleaning, manipulating, and preparing the Bellabeat dataset for further analysis in R. The dataset was thoroughly checked for data quality issues, duplicates, and null values. Relevant columns were added to enhance the analysis, and the tables were renamed for consistency.

The dataset is sourced from Kaggle and contains personal tracker data from 30 Fitbit users who consented to share their data.
The data includes minute-level output for physical activity, heart rate, and sleep monitoring.
The dataset spans from April 12, 2016, to May 12, 2016, providing a single month of data.


## Uploading Data to BigQuery

The dataset was uploaded to Google BigQuery for cleaning and manipulation.
Issues with datetime formatting were encountered and resolved for the hourlycalories, hourlyintensities, sleepday, and weightlog tables.
Tables were created in BigQuery for each CSV file.

## Checking Distinct User IDs
```sql
Queried the count of distinct user IDs in the dailyactivity table:
sqlCopy codeSELECT DISTINCT Id 
FROM `bamboo-life-418613.bellabeat.dailyactivity` LIMIT 1000
```
#### *Result: 33 distinct IDs in the dailyactivity table.*


#### Checking the consistency of distinct user IDs across all tables:
 ```sql
 SELECT
  (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.dailyactivity`) AS DailyActivityCount,
  (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.dailycalories`) AS DailyCaloriesCount,
  (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.dailyintensities`) AS DailyIntensitiesCount,
  (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.dailysteps`) AS DailyStepsCount,
  (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.hourlyintensities`) AS HourlyIntensitiesCount,
  (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.hourlysteps`) AS HourlyStepsCount,
  (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.sleepday`) AS SleepDayCount,
  (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.weightlog`) AS WeightLogCount,
  CASE
    WHEN
      (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.dailyactivity`) =
      (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.dailycalories`) AND
      (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.dailycalories`) =
      (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.dailyintensities`) AND
      (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.dailyintensities`) =
      (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.dailysteps`) AND
      (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.dailysteps`) =
      (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.hourlyintensities`) AND
      (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.hourlyintensities`) =
      (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.hourlysteps`) AND
      (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.hourlysteps`) =
      (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.sleepday`) AND
      (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.sleepday`) =
      (SELECT COUNT(DISTINCT Id) FROM `bamboo-life-418613.bellabeat.weightlog`)
    THEN 'Counts are consistent across all tables'
    ELSE 'Counts differ across some tables'
  END AS ConsistencyCheck
``` 
## Result: Counts differed across some tables.

| Dataset           | Distinct IDs |
|-------------------|--------------|
| dailyactivity     | 33           |
| dailycalories     | 33           |
| dailyintensities  | 33           |
| dailysteps        | 33           |
| hourlyintensities | 33           |
| hourlysteps       | 33           |
| sleepday          | 24           |
| weightlog         | 8            |



# Checking for Duplicates

Duplicate check - dailycalories
```sql
SELECT Id, ActivityDay, Calories, Count(*)
FROM bellabeat.dailycalories
GROUP BY Id, ActivityDay, Calories
HAVING Count(*) > 1;
```

Duplicate check - dailyactivity
```sql SELECT Id, ActivityDate, TotalSteps, Count(*)
FROM bellabeat.dailyactivity
GROUP BY Id, ActivityDate, TotalSteps
HAVING Count(*) > 1;
```

Duplicate check - dailyintensities
```sql
SELECT Id,
      ActivityDay,
      SedentaryMinutes,
      LightlyActiveMinutes,
      FairlyActiveMinutes,
      VeryActiveMinutes,
      SedentaryActiveDistance,
      LightActiveDistance,
      ModeratelyActiveDistance,
      VeryActiveDistance,
      COUNT(*)
FROM `bellabeat.dailyintensities`
GROUP BY Id,
        ActivityDay,
        SedentaryMinutes,
        LightlyActiveMinutes,
        FairlyActiveMinutes,
        VeryActiveMinutes,
        SedentaryActiveDistance,
        LightActiveDistance,
        ModeratelyActiveDistance,
        VeryActiveDistance
HAVING COUNT(*) > 1;
```

Duplicate check - hourlysteps
```sql
SELECT Id, ActivityHour, StepTotal, Count(*)
FROM `bellabeat.hourlysteps`
GROUP BY Id, ActivityHour, StepTotal
HAVING Count(*) > 1;
```
Duplicate check - sleepday
```sql
SELECT Id, 
       SleepDay,
       TotalSleepRecords,
       TotalMinutesAsleep,
       TotalTimeInBed, 
       Count(*)
FROM `bellabeat.sleepday`
GROUP BY Id, 
         SleepDay,
         TotalSleepRecords,
         TotalMinutesAsleep,
         TotalTimeInBed
HAVING Count(*) > 1;
```
Result: Duplicates were found only in sleepday table.

| Id         | SleepDay                    | TotalSleepRecords | TotalMinutesAsleep | TotalMinutesInBed | Count of Duplicates |
|------------|-----------------------------|-------------------|--------------------|-------------------|---------------------|
| 4388161847 | 2016-05-05 00:00:00.000000 UTC | 1                 | 471                | 495               | 2                   |
| 4702921684 | 2016-05-07 00:00:00.000000 UTC | 1                 | 520                | 543               | 2                   |
| 8378563200 | 2016-04-25 00:00:00.000000 UTC | 1                 | 388                | 402               | 2                   |


Verified the duplicates in the sleepday table:

```sql
SELECT *
FROM `bellabeat.sleepday`
WHERE (Id = 4388161847 AND SleepDay = "2016-05-05 00:00:00.000000 UTC")
  OR (Id = 4702921684 AND SleepDay = "2016-05-07 00:00:00.000000 UTC")
  OR (Id = 8378563200 AND SleepDay = "2016-04-25 00:00:00.000000 UTC");

Removed duplicates from the sleepday table and created a new clean_sleepday table:
sqlCopy codeCREATE OR REPLACE TABLE `bellabeat.clean_sleepday` AS
SELECT *
FROM (
  SELECT
    *,
    ROW_NUMBER() OVER (PARTITION BY Id, SleepDay ORDER BY Id) as row_num
  FROM `bellabeat.sleepday`
)
WHERE row_num = 1;
```
| Id         | SleepDay                  | TotalSleepRecords | TotalMinutesAsleep | TotalTimeInBed |
|------------|---------------------------|-------------------|--------------------|----------------|
| 4388161847 | 2016-05-05 00:00:00 UTC   | 1                 | 471                | 495            |
| 4388161847 | 2016-05-05 00:00:00 UTC   | 1                 | 471                | 495            |
| 4702921684 | 2016-05-07 00:00:00 UTC   | 1                 | 520                | 543            |
| 4702921684 | 2016-05-07 00:00:00 UTC   | 1                 | 520                | 543            |
| 8378563200 | 2016-04-25 00:00:00 UTC   | 1                 | 388                | 402            |
| 8378563200 | 2016-04-25 00:00:00 UTC   | 1                 | 388                | 402            |


## Checking for Null Values

Null value check - weightlog
```sql
SELECT
  COUNT(*) AS total_rows,
  COUNTIF(WeightKg IS NULL) AS null_WeightKg,
  COUNTIF(WeightPounds IS NULL) AS null_WeightPounds,
  COUNTIF(Fat IS NULL) AS null_Fat,
  COUNTIF(BMI IS NULL) AS null_BMI,
  COUNTIF(IsManualReport IS NULL) AS null_report,
  COUNTIF(LogId IS NULL) AS null_logId,
FROM `bamboo-life-418613.bellabeat.weightlog`;
```

Result: Null values were found in the Fat column of the weightlog table.


Checked for null values in remaining tables:
dailyactivity, clean_sleepday, dailycalories, dailyintensities, dailysteps, hourlyintensities, hourlysteps

No null values were found in these tables.



# Dropping the weightlog Table

The weightlog table was dropped from the analysis due to the following reasons:

1. Incomplete data: Only 8 unique IDs, which may not provide a comprehensive view of user behavior and patterns.
2. Missing values: High number of null values in the Fat column, suggesting unreliable data capture or reporting.
3. Relevance: Weight data was not considered a major variable for the analysis.


## New Columns

Adding Time of Day Column:  I added a DayPeriod column to the hourlyintensities and hourlysteps tables to categorize data into "Morning" (6 AM to 11:59 AM), "Afternoon" (12 PM to 3:59 PM), and "Night" (4 PM to 5:59 AM):

```sql
SELECT *, 
   CASE 
      WHEN EXTRACT(HOUR FROM ActivityHour) BETWEEN 6 AND 11 THEN 'Morning'
      WHEN EXTRACT(HOUR FROM ActivityHour) BETWEEN 12 AND 15 THEN 'Afternoon'
      ELSE 'Night'
   END AS DayPeriod
FROM bellabeats.hourlyintensities;
```

```sql
SELECT *, 
   CASE 
      WHEN EXTRACT(HOUR FROM ActivityHour) BETWEEN 6 AND 11 THEN 'Morning'
      WHEN EXTRACT(HOUR FROM ActivityHour) BETWEEN 12 AND 15 THEN 'Afternoon'
      ELSE 'Night'
   END AS DayPeriod
FROM bellabeats.hourlysteps;
```
The resulting tables were saved as:
| hourlystepstod | hourlyintensitiestod |
|-------------------|--------------|

### Adding Day of Week Column

Added a DayOfWeek column to the dailysteps, dailyintensities, dailycalories, dailyactivity, and clean_sleepday tables using the FORMAT_DATE() function:

```sql
SELECT *, 
    FORMAT_DATE('%A', ActivityDay) AS DayOfWeek
FROM bellabeat.dailysteps;
sqlCopy codeSELECT 
    *, 
    FORMAT_DATE('%A', ActivityDay) AS DayOfWeek
FROM bellabeat.dailyintensities;
sqlCopy codeSELECT 
    *, 
    FORMAT_DATE('%A', ActivityDay) AS DayOfWeek
FROM bellabeat.dailycalories;
sqlCopy codeSELECT
   *,
   FORMAT_DATE('%A', ActivityDate) AS DayOfWeek
FROM `bellabeat.dailyactivity`;
sqlCopy codeSELECT 
    *, 
    FORMAT_DATE('%A', SleepDay) AS DayOfWeek
FROM bellabeat.clean_sleepday;
```

The resulting tables were saved as:
| dailystepsdow| dailyintensititesdow | dailycaloriesdow | TotalMinutesAsleep |
|------------|---------------------------|-------------------|--------------------|

### Renaming Columns for Consistency

Renamed the ActivityDate column to ActivityDay in the dailyactivitydow table:
```sql
ALTER TABLE bellabeat.dailyactivitydow
RENAME COLUMN ActivityDate TO ActivityDay;
```
Renaming the SleepDay column to ActivityDay in the clean_sleepdaydow table:
```sql
ALTER TABLE bellabeat.clean_sleepdaydow
RENAME COLUMN SleepDay TO ActivityDay;
```

# Data Export

| Original Dataset         | Renamed Dataset           |
|--------------------------|---------------------------|
| clean_sleepdaydow        | sleepdayclean             |
| dailyactivitydow         | dailyactivityclean        |
| dailycaloriesdow         | dailycaloriesclean        |
| dailyintensitiesdow      | dailyintensitiesclean     |
| dailystepsdow            | dailystepsclean           |
| hourlyintensitiestod     | hourlyintensitiesclean    |
| hourlystepstod           | hourlystepsclean          |

