# Bellabeat Data Analysis

## Introduction
The Bellabeat data analysis project focuses on analyzing user data to gain insights and inform marketing strategies for Bellabeat, a high-tech manufacturer of health-focused products for women. Through my analysis, I will extensively explore relationships and patterns within historical health tech data. My aim is to use my findings, along with research papers and industry reports, to develop a new data-backed marketing strategy for Bellabeat’s stakeholders.

## Documentation
[SQL Cleaning & Manipulation Markdown](https://github.com/karammulc/bella/blob/main/SQL%20Cleaning%20%26%20Manipulation.md)

[R Manipulation, Analysis & Visualizations Markdown](https://github.com/karammulc/bella/edit/main/R%20Manipulation%2C%20Analysis%20%26%20Visualizations.md)

[Correlation & Regression Markdown](https://github.com/karammulc/bella/blob/main/Correlation%20%26%20Linear%20Regressions.md) 



## Table of Contents
- [Introduction](#introduction)
- [Documentation](#documentation)
- [Data Source](#data-source)
- [Data Storage](#data-storage)
- [Challenges](#challenges)
- [Key Findings](#key-findings)
- [Target Market](#target-market)
- [Recommendations](#recommendations)
- [Tables](#tables)
- [Visualizations](#visualizations)
- [Relevant Studies](#relevant-studies)
- [User Average Activity](#user-average-activity)
- [Statistical Analysis](#statistical-analysis)

## Data Source
- The dataset is sourced from Kaggle and contains personal tracker data from 30 Fitbit users who consented to share their data.
- The data includes minute-level output for physical activity, heart rate, and sleep monitoring.
- The dataset spans from April 12, 2016, to May 12, 2016, providing a single month of data.
- For this project I used 10 out of 18 CSV files.


## Data Storage
- The datasets have been stored locally in a folder named bellabeat_export_4.12.16-5.12.16 & duplicated for retention of the original data prior to cleaning and manipulation. 
- Files were uploaded to BigQuery with new table names recorded within documentation.



## Challenges

#### Data Limitations and Considerations


The dataset used in this analysis spans from April 12, 2016, to May 12, 2016, providing only one month of data. This limited timeframe may restrict the scope and generalizability of the findings. Additionally, the 30 user sample size is small, and the methods of participant selection through Amazon Mechanical Turk are not detailed on Kaggle, which introduces potential bias into the analysis. It is also important to note that the data is from 2016 and may not reflect the most recent trends or user behaviors. Furthermore, the lack of demographic information limits the depth and context of the insights that can be derived from this analysis.


#### Consistency


Although the Fitbit Fitness Tracker Dataset description claims that thirty eligible Fitbit users consented to the use of their data, 33 distinct IDs were found within the records during exploration.

The weightlog table was the most explicit example of this dataset’s challenges and was subsequently dropped:
Incomplete data: Only 8 unique IDs, which may not provide a comprehensive view of user behavior and patterns.
Missing values: High number of null values in the Fat column, suggesting unreliable data capture or reporting.
Relevance: Weight data was not considered a major variable for the analysis.


Datetime Formatting
While uploading the CSVs into BigQuery there was an error uploading hourlycalories, hourlyintensities, sleepday, weightlog due to issues with datetime formatting. This was resolved within google sheets.  I renamed each changed file with v2.


## Key Findings
- There is a strong positive correlation between total steps and calories burned for both weekdays and weekends.
- As total steps increase, calories burned also tends to increase.
- There is a strong positive correlation between very active minutes and calories burned for both weekdays and weekends. As very active minutes increase, calories burned also tends to increase.
- There is a significant negative correlation between sedentary minutes and total time in bed for both weekdays and weekends. As total time in bed increases, sedentary minutes tend to decrease.
- On average, people exercise with the greatest intensity during afternoon workouts, followed by mornings, with the lowest intensity in the evenings.
- On average, people take more steps during the afternoon, followed by mornings, with the lowest step count in the evenings.

## Target Market

Bellabeat's target market comprises health-conscious, tech-savvy women who prioritize a holistic approach to wellness. They value integrating health into their daily lives, aligning their routines with their menstrual cycles, and being part of a supportive community. These women appreciate innovative, data-driven solutions and stylish, functional products, spanning a diverse age range from young adults to older women.

