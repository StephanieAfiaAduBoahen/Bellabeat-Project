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

Change column names to lowecases
```{r}
clean_names(dailyActivity_merged)
dailyActivity_merged <- rename_with(dailyActivity_merged, tolower)
clean_names(hourlySteps_merged)
hourlySteps_merged <- rename_with(hourlySteps_merged, tolower)
clean_names(sleepDay_merged)
sleepDay_merged <- rename_with(sleepDay_merged, tolower)
```

Convert date string of hourlySteps_merged to date time and add a column for hours
```{r}
hourlySteps_merged$activityhour=as.POSIXct(hourlySteps_merged$activityhour,format="%m/%d/%Y %I:%M:%S %p")
hourlySteps_merged$hour <-  format(hourlySteps_merged$activityhour,format= "%H")
head(hourlySteps_merged)
```

Add week column to dailyActivity_merged
```{r}
dailyActivity_merged <- dailyActivity_merged %>% 
  mutate( weekday = weekdays(as.Date(ActivityDate, "%m/%d/%Y")))
```

Merge dailyActivity_merged and sleepDay_merged
```{r}
daily_act_sleep <- merge(dailyActivity_merged, sleepDay_merged, by = "id")
```

Verify data in merged datasets
```{r}
sum(is.na(daily_act_sleep))
sum(duplicated(daily_act_sleep))
n_distinct(daily_act_sleep$Id)
colnames(daily_act_sleep)
```
Order days of the week from sunday to satarday
```{r}
daily_act_sleep$weekday <- ordered(daily_act_sleep$weekday, levels=c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))
daily_act_sleep[order(daily_act_sleep$weekday), ]
```

## ANALYZE
For the daily_act_sleep dataframe summary
```{r}
daily_act_sleep %>%
  select(totalsteps,
         totaldistance,
         veryactiveminutes,
         sedentaryminutes,
         calories,
         totalsleeprecords,
         totalminutesasleep,
         totaltimeinbed) %>%
  summary()
```

Summary of hourlySteps_merged
```{r}
hourlySteps_merged %>%
  select(steptotal) %>%
  summary()
```

Visualize steps per week: From the anlaysis, the women had more steps on Tuesdays.
```{r}
ggplot(data=daily_act_sleep, aes(x=weekday, y=totalsteps, fill=weekday))+ 
  geom_bar(stat="identity", fill="pink")+
  labs(title="Steps per weekday", y="Total Steps")
```
![Steps per weekday](https://github.com/StephanieAfiaAduBoahen/Bellabeat-Projects/assets/158788793/4315e403-7ce2-42aa-9b5a-f400142040f3)

Visualize calories burnt during the week: More calories was burnt on Tuesdays. I assume since the highest steps is on tuesday, it shows a positive correlation.
```{r}
ggplot(data=daily_act_sleep, aes(x=weekday, y=calories, fill=weekday))+ 
  geom_bar(stat="identity", fill="blue")+
  labs(title="Calories burnt during the week")
```
![Calories burnt](https://github.com/StephanieAfiaAduBoahen/Bellabeat-Projects/assets/158788793/7e7b811d-21e4-4c40-b96b-092020690050)

Visualize Very active minutes: Users were very active on Tuesday.
```{r}
ggplot(data=daily_act_sleep, aes(x=weekday, y=veryactiveminutes, fill=weekday))+ 
  geom_bar(stat="identity", fill="Orange")+
  labs(title="Very Active Minutes", y="Very Active Minutes")
```
![Active Minutes](https://github.com/StephanieAfiaAduBoahen/Bellabeat-Projects/assets/158788793/f28052ec-f399-415c-9995-a8650fbe4f60)

Visualize sedentary minutes: Tuesday has the highest sedentary minutes
```{r}
ggplot(data=daily_act_sleep, aes(x=weekday, y=sedentaryminutes, fill=weekday))+ 
  geom_bar(stat="identity", fill="red")+
  labs(title="Sedentary Minutes", y="Sedentary Minutes")
```
![Sedentary Minutes](https://github.com/StephanieAfiaAduBoahen/Bellabeat-Projects/assets/158788793/3c14f644-2634-42d9-8061-647dc0b67692)

Visualize total minutes asleep: From the analysis, the women slept more on Tuesdays, wednesdays and Thursdays. I assume since these are workdays, they are tired from their daily jobs and need more rest.
```{r}
ggplot(data=daily_act_sleep, aes(x=weekday, y=totalminutesasleep, fill=weekday))+ 
  geom_bar(stat="identity", fill="purple")+
  labs(title="Total Minutes Asleep During the Week", y="Total Minutes Asleep")
```
![Total Minutes Asleep](https://github.com/StephanieAfiaAduBoahen/Bellabeat-Projects/assets/158788793/3c368969-6349-4c25-8ae5-8f3ed4f7dc54)

Visualize total distance: From the analyis, the women cover more distance on Tuesdays and less on Sundays.
```{r}
ggplot(data=daily_act_sleep, aes(x=weekday, y=totaldistance, fill=weekday))+ 
  geom_bar(stat="identity", fill= "green")+
  labs(title = "Total distance covered", y= "Total Distance")
```
![Total distance covered](https://github.com/StephanieAfiaAduBoahen/Bellabeat-Projects/assets/158788793/96b0cb61-7365-485c-8a5f-53483f5c8d49)

Visualize hourly steps: Most users are active from 8am to 8pm. This may mean that they work around these times and therefore achieve more steps. The highest hourly steps is around 6pm-7pm. I assume they workout or take a walk/run around this time.
```{r}
ggplot(data=hourlySteps_merged, aes(x=hour, y=steptotal, fill=hour))+
  geom_bar(stat="identity")+
  labs(title="Hourly Steps")
```
![hourly steps](https://github.com/StephanieAfiaAduBoahen/Bellabeat-Projects/assets/158788793/ef41c0e0-978b-4e10-a09e-0816c8d2a575)

Visualize total steps and calories: It shows a positive correlation between total steps and calories. This means they move in the same direction.
```{r}
ggplot(data = daily_act_sleep, aes(x = totalsteps, y = calories)) + 
  geom_point() + geom_smooth() + labs(title ="Total Steps vs. Calories")
```
![total cal vs steps](https://github.com/StephanieAfiaAduBoahen/Bellabeat-Projects/assets/158788793/e18663cd-e0c6-4523-8464-b6fa1a522d4c)


## SHARE

## ACT
Conclusion:
1.	Most users were active on Tuesdays and less active on Sundays.
2.	The Women were more active during working hours 8am-8pm
3.	The average sleep duration was 419.1 minutes which is approximately 7 hours. Experts recommend adults to sleep between 7-9 hours per night.

Recommendation:
1.	To promote healthier lifestyles, create features that motivate consumers to engage in more physical activity and get sufficient rest.
2.	Provide education and further information about adopting healthy habits.
3.	A larger sample size should be used to avoid bias.
4.	More information should be collected such as age.






