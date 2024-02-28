# Bellabeat-Projects

## ASK
You are a junior data analyst working on the marketing analyst team at Bellabeat, a high-tech manufacturer of health-focused
products for women. Bellabeat is a successful small company, but they have the potential to become a larger player in the
global smart device market. Urška Sršen, cofounder and Chief Creative Officer of Bellabeat, believes that analyzing smart
device fitness data could help unlock new growth opportunities for the company. You have been asked to focus on one of
Bellabeat’s products and analyze smart device data to gain insight into how consumers are using their smart devices. The
insights you discover will then help guide marketing strategy for the company. You will present your analysis to the Bellabeat
executive team along with your high-level recommendations for Bellabeat’s marketing strategy.

## PREPARE
DATA ORIGINATED FROM- https://www.kaggle.com/datasets/arashnic/fitbit?resource=download
This Kaggle data set contains personal fitness tracker from thirty fitbit users. Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. It includes information about daily activity, steps, and heart rate that can be used to explore users’ habits.

## PROCESS
### CLEAN DATA
Cheak For Duplicates
```{r}
sum(is.na(dailyActivity_merged))
sum(is.na(hourlySteps_merged))
sum(is.na(sleepDay_merged))
```

Remove duplicates from sleepDay_merged
```{r}
sleepDay_merged <- sleepDay_merged[!duplicated(sleepDay_merged), ]
nrow(sleepDay_merged[duplicated(sleepDay_merged), ])
```

## Change column names to lowecases
```{r}
clean_names(dailyActivity_merged)
dailyActivity_merged <- rename_with(dailyActivity_merged, tolower)
clean_names(hourlySteps_merged)
hourlySteps_merged <- rename_with(hourlySteps_merged, tolower)
clean_names(sleepDay_merged)
sleepDay_merged <- rename_with(sleepDay_merged, tolower)
```

## Convert date string of hourlySteps_merged to date time and add a column for hours
```{r}
hourlySteps_merged$activityhour=as.POSIXct(hourlySteps_merged$activityhour,format="%m/%d/%Y %I:%M:%S %p")
hourlySteps_merged$hour <-  format(hourlySteps_merged$activityhour,format= "%H")
head(hourlySteps_merged)
```

## Add week column to dailyActivity_merged
```{r}
dailyActivity_merged <- dailyActivity_merged %>% 
  mutate( weekday = weekdays(as.Date(ActivityDate, "%m/%d/%Y")))
```

## Merge dailyActivity_merged and sleepDay_merged
```{r}
daily_act_sleep <- merge(dailyActivity_merged, sleepDay_merged, by = "id")
```

## Verify data in merged datasets
```{r}
sum(is.na(daily_act_sleep))
sum(duplicated(daily_act_sleep))
n_distinct(daily_act_sleep$Id)
colnames(daily_act_sleep)
```
## Order days of the week from sunday to satarday
```{r}
daily_act_sleep$weekday <- ordered(daily_act_sleep$weekday, levels=c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))
daily_act_sleep[order(daily_act_sleep$weekday), ]
```









