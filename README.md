# Cyclistic_Case_Study_for_Data_Analysis

**Title:** Cyclistics Data Analysis

**Author:** Kareem Mohamed

**Date:** 15-3-2024

## Scenario
Cyclistic, a bike-share company in Chicago. The director of marketing believes the company’s future success depends on maximizing the number of annual memberships. Therefore, your team wants to understand how casual riders and annual members use Cyclistic bikes differently y. From these insights the marketing anakyst team at cyclistic that im a part of will design a new marketing strategy to convert casual riders into annual members.

## About The Company
In 2016, Cyclistic launched a successful bike-share offering. Since then, the program has grownto a fleet of 5,824 bicycles that are geotracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system anytime.

Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Cyclistic’s finance analysts have concluded that annual members are much more profitable
than casual riders. Moreno believes that maximizing the number of annual members will be key to future growth.
# Ask

**Business Task**

Design marketing strategies aimed at converting casual riders into annual members by analyzing the historical bike trip data given by Cyclistic to identify trends.

**Key Stakeholder**
- Lily Moreno <- Director of marketing and my manager.
- Cyclistic Marketing Team <- A team of data analysts who are responsible for collecting, analyzing, and reporting data that helps guide Cyclistic marketing strategy.
- Cyclistic Executive Team <- The notoriously detail-oriented executive team will decide whether to approve the recommended marketing program.

# Prepare 
Cyclistic’s historical trip data, this is a public data that i used to explore how different customer types are using Cyclistic bikes, the data is located [here](https://divvy-tripdata.s3.amazonaws.com/index.html). It is stored in csv file format and it contains 12 files for the past 12 month starting from February 2024 and going back to March 2023.

The data has some limitations :

It has a lot of empty cells in the start_station and end_station as well as ther station_id.

Data-privacy issues prohibit me from using riders’ personally identifiable information. This means that i won’t be able to connect pass purchases to credit card numbers to determine if casual riders live in the Cyclistic service area or if they have purchased multiple single passes.

Im going to check the data using the ROCCC aproach
- **Reliable :** The data is reliable as it comes from Bike Share that operates the City of Chicago, the city permets Bike Share to make certain Divvy system data owned by the City available to the public.
- **Original :** The data was gathered and released to the public by Motivate Internation Inc, which makes the data original.
- **Comprehensive :** The data is organized in long format and contains various columns as rider_id, rideable_type, started_at, ended_at, start_staion_name, end_station_name, start_station_id, end_station_id, member_causal.
- **Current :** The data is up to date as it has data for the past 12 month till February.
- **Cited :** Bikeshare hereby grants a non-exclusive, royalty-free, limited, perpetual license to access, reproduce, analyze, copy, modify, distribute in your product or service and use the Data for any lawful purpose [License](https://www.divvybikes.com/data-license-agreement).

# Process
What tools did i use ? and Why?

I used Excel and SQL and R to check and clean and manipulate the data but the analyzing was done mostly on R and a little bit on SQL. The data contains a lot of information that R and SQL would handel better than Excel so thats why i used them in manipulating and cleaning most of the time.

### Documentaion of data cleaning in Excel :
--------------------------------------------
- I added column called days_of_week and i calculated the day of the week that each ride started using the WEEKDAY command
- Then i added a column called ride_length to calculate the length of each ride by subtracting the column ended_at from the column started_at.
- I checked the data for duplicates but none of them had any duplicates.
### Documentation of data cleaning in R :
-------------------------------------------
- **Inspecting**
  
I start by inspecting various data frames using `glimpse()`, `str()` and `colnames()`.
```{r}
glimpse(february24)
str(march23)
colnames(october23)
```
- **Combining Data**
  
Combining all the 12 month using `bind_rows()` in one data frame to use it in further analysis.
The total entries are {4,332,407}.
```{r}
combined_bike_data <- bind_rows(february24, jan24, december23, november23, october23, september23, august23, july23, june23, may23, april23, march23)
```
- **Adding N/A to empty cells**
  
First i will add N/A to all the empty cells in all data frames to see how many they are.
```{r}
march23[march23 == ""] <- NA
april23[april23 == ""] <- NA
may23[may23 == ""] <- NA
june23[june23 == ""] <- NA
july23[july23 == ""] <- NA
august23[august23 == ""] <- NA
september23[september23 == ""] <- NA
october23[october23 == ""] <- NA
november23[november23 == ""] <- NA
december23[december23 == ""] <- NA
jan24[jan24 == ""] <- NA
february24[february24 == ""] <- NA
```
- **Deleting N/A Values**
  
After deleting the N/A values here is a summary of what was deleted:

-March {58,231} rows were deleted and {200,447} are now the available entries.

-April {102,371} row were deleted and {324,197} are now the available entries.

-May {141,600} row were deleted and {463,227} are now the available entries.

-June {184,860} row were deleted and {534,758} are now the available entries.

-July {193,692} row were deleted and {573,958} are now the available entries.

-August {186,774} row were deleted and {584,919} are now the available entries.

-September {195,736} row were deleted and {506,635} are now the available entries.

-October {133,332} row were deleted and {403,781} are now the available entries.

-November {87,720} row were deleted and {274,798} are now the available entries.

-December {56,930} row were deleted and {167,143} are now the available entries.

-Jan24 {31,065} row were deleted and {113,808} are now the available entries.

-Feb24 {38,428} row were deleted and {184,736} are now the available entries.
```{r}
march23 <- na.omit(march23)
april23 <- na.omit(april23)
may23 <- na.omit(may23)
june23 <- na.omit(june23)
july23 <- na.omit(july23)
august23 <- na.omit(august23)
september23 <- na.omit(september23)
october23 <- na.omit(october23)
november23 <- na.omit(november23)
december23 <- na.omit(december23)
jan24 <- na.omit(jan24)
february24 <- na.omit(february24)
```
- **Removing columns**

Removing `start_lat`, `start_lng`, `end_lat`, and `end_lng` from each month and the combined data as they wont be needed in the analysis.
```{r}
combined_bike_data <- combined_bike_data %>% select(-c(start_lat, start_lng, end_lat, end_lng))
february24 <- february24 %>% select(-c(start_lat, start_lng, end_lat, end_lng))
jan24 <- jan24 %>% select(-c(start_lat, start_lng, end_lat, end_lng))
december23 <- december23 %>% select(-c(start_lat, start_lng, end_lat, end_lng))
november23 <- november23 %>% select(-c(start_lat, start_lng, end_lat, end_lng))
october23 <- october23 %>% select(-c(start_lat, start_lng, end_lat, end_lng))
september23 <- september23 %>% select(-c(start_lat, start_lng, end_lat, end_lng))
august23 <- august23 %>% select(-c(start_lat, start_lng, end_lat, end_lng))
july23 <- july23 %>% select(-c(start_lat, start_lng, end_lat, end_lng))
june23 <- june23 %>% select(-c(start_lat, start_lng, end_lat, end_lng))
april23 <- april23 %>% select(-c(start_lat, start_lng, end_lat, end_lng))
march23 <- march23 %>% select(-c(start_lat, start_lng, end_lat, end_lng))
```
# Analyze
Using `count()` function to know how many riders are `casual` in each month.

You can see
- February had {38,170} 
- Jan {17,713}
- December {36,686}
- November {72,097}
- October {130,300}
- September {196,964}
- August {233,856}
- July {245,294}
- June {219,794}
- May {177,039}
- April {110,538}
- March {46,792}
- 
The casual riders numbers start to increase starting in April and the most casual riders occur during the summer in June, July, August those 3 month have the highest casual riders for the past 12 month, and then the riders count goes down starting from November.
```{r}
sum(february24$member_casual == "casual")
sum(jan24$member_casual == "casual")
sum(december23$member_casual == "casual")
sum(november23$member_casual == "casual")
sum(october23$member_casual == "casual")
sum(september23$member_casual == "casual")
sum(august23$member_casual == "casual")
sum(july23$member_casual == "casual")
sum(june23$member_casual == "casual")
sum(may23$member_casual == "casual")
sum(april23$member_casual == "casual")
sum(march23$member_casual == "casual")
```
Next we will analyze the `members` and see the difference between them and the casual riders.

- February had {146,566}
- Jan {96,095}
- December {130,457}
- November {202,701}
- October {273,481}
- September {309,671}
- August {351,063}
- July {328,664}
- June {314,964}
- May {286,188}
- April {213,659}
- March {153,655}
```{r}
sum(february24$member_casual == "member")
sum(jan24$member_casual == "member")
sum(december23$member_casual == "member")
sum(november23$member_casual == "member")
sum(october23$member_casual == "member")
sum(september23$member_casual == "member")
sum(august23$member_casual == "member")
sum(july23$member_casual == "member")
sum(june23$member_casual == "member")
sum(may23$member_casual == "member")
sum(april23$member_casual == "member")
sum(march23$member_casual == "member")
```
Using the `Filter()` function on the `combined_bike_data` to compare between electric bikes and classic bikes when the ride length exceeds one hour and when it is less than one hour.

In f1  i filtered the ride length when it is more than or equal to one hour and the `rideable_type` is `electric_bike` the filtering shows that only {18,909} users use the electric bike for more than an hour.

In f2 i filtered the ride_length when it is more than or equal to one hour and the `rideable_type` is `classic_bike` the filtering shows that {83,170} users use classic bikes which means that that classic bikes are more used in longer hours.

Then i used filter function again to see when the `ride_length` is lower than one hour what is the difference.

In f3 i used filtering for `electric_bike` and it shows {1,514,184} rides made using electric bikes when it less than an hour.

In f4 i used filtering for `classic_bike` and it shows {2,643,853} rides made using it.

This concludes that classic bikes are used more when the length is less than one hour so overall `classic_bike` is used more than `electric_bike` 

```{r}
f1 <- combined_bike_data %>%
  filter(ride_length >= "01:00:00") %>%
  filter(rideable_type == "electric_bike")
  
f2 <- combined_bike_data %>%
  filter(ride_length >= "01:00:00") %>%
  filter(rideable_type == "classic_bike")

f3 <- combined_bike_data %>%
  filter(ride_length < "01:00:00") %>%
  filter(rideable_type == "electric_bike")

f4 <- combined_bike_data %>%
  filter(ride_length < "01:00:00") %>%
  filter(rideable_type == "classic_bike")
```






