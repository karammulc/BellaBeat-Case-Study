### The statistical significance should be considered limited in the context of the small sample size of 30 users.



#### Relationship between Very Active Minutes and Calories Burned:
```{r}
cor_test_active_calories <- cor.test(merged_sleepday_dailyactivity$VeryActiveMinutes, merged_sleepday_dailyactivity$Calories)
print(cor_test_active_calories)

lm_active_calories <- lm(Calories ~ VeryActiveMinutes, data = merged_sleepday_dailyactivity)
summary(lm_active_calories)
```

#### Relationship between Total Steps and Calories Burned:
```{r}
cor_test_steps_calories <- cor.test(merged_sleepday_dailyactivity$TotalSteps, merged_sleepday_dailyactivity$Calories)
print(cor_test_steps_calories)

lm_steps_calories <- lm(Calories ~ TotalSteps, data = merged_sleepday_dailyactivity)
summary(lm_steps_calories)
```

#### Relationship between Sedentary Minutes and Total Time in Bed:
```{r}
cor_test_sedentary_sleep <- cor.test(merged_sleepday_dailyactivity$SedentaryMinutes, merged_sleepday_dailyactivity$TotalTimeInBed)
print(cor_test_sedentary_sleep)

lm_sedentary_sleep <- lm(TotalTimeInBed ~ SedentaryMinutes, data = merged_sleepday_dailyactivity)
summary(lm_sedentary_sleep)
```

#### Relationship between Total Distance and Total Minutes Asleep:
```{r}
cor_test_sedentary_sleep <- cor.test(merged_sleepday_dailyactivity$SedentaryMinutes, merged_sleepday_dailyactivity$TotalTimeInBed)
print(cor_test_sedentary_sleep)

lm_sedentary_sleep <- lm(TotalTimeInBed ~ SedentaryMinutes, data = merged_sleepday_dailyactivity)
summary(lm_sedentary_sleep)
```

#### Relationship between Very Active Minutes and Total Minutes Asleep:
```{r}
cor_test_active_sleep <- cor.test(merged_sleepday_dailyactivity$VeryActiveMinutes, merged_sleepday_dailyactivity$TotalMinutesAsleep)
print(cor_test_active_sleep)

lm_active_sleep <- lm(TotalMinutesAsleep ~ VeryActiveMinutes, data = merged_sleepday_dailyactivity)
summary(lm_active_sleep)
```
