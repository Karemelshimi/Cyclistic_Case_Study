# Cyclistic_Case_Study_for_Data_Analysis

**Title:** Cyclistics Data Analysis

**Author:** Kareem Mohamed

**Date:** 15-3-2024


## Reference

<a href="#ask" id="toc-ask">Ask</a>

<a href="#prepare" id="toc-prepare">Prepare</a>

<a href="#process" id="toc-process">Process</a>

<a href="#analyze" id="toc-analyze">Analyze</a>

<a href="#share" id="toc-share">Share</a>








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
- I checked the data for duplicates but none of them had any duplicates.
- I changed each file name by naming it based on the month and year that it contains data for, so fo example "202402-divvy-tripdata" is now called "february24-divvy-tripdata".
### Documentation of data cleaning in R :
-------------------------------------------
-**Importing Data**

Importing the data that i downloaded and combining it in one data frame called `combined_bike_data`, the data frame will combine 12 csv files containing historical data about the past 12 month of Chicago city bike riders starting from February 2024 going back to March 2023.

```{r setup, include=FALSE}
knitr::opts_knit$set(root.dir = "D:/Coursera Data Analysis/Cyclistic")
```

After combining the data the total observations are {5,707,168} and it has {15} variables.

```{r} 
combined_bike_data <- rbind(
read.csv("february24-divvy-tripdata.csv"),
read.csv("january24-divvy-tripdata.csv"),
read.csv("december23-divvy-tripdata.csv"),
read.csv("november23-divvy-tripdata.csv"),
read.csv("october23-divvy-tripdata.csv"),
read.csv("september23-divvy-tripdata.csv"),
read.csv("august23-divvy-tripdata.csv"),
read.csv("july23-divvy-tripdata.csv"),
read.csv("june23-divvy-tripdata.csv"),
read.csv("may23-divvy-tripdata.csv"),
read.csv("april23-divvy-tripdata.csv"),
read.csv("march23-divvy-tripdata.csv")
)
```
- **Inspecting**
  
I start by inspecting various data frames using `glimpse()`, `str()` and `colnames()`.
```{r}
glimpse(combined_bike_data)
colnames(combined_bike_data)
```

- **Adding N/A to empty cells**
  
Some cells in the data frame are empty they don't have N/A values so in order to delete them or to acknowledge them i will add `NA` to them.
```{r}
combined_bike_data$start_station_name[combined_bike_data$start_station_name == ""] <- NA
combined_bike_data$start_station_id[combined_bike_data$start_station_id == ""] <- NA
combined_bike_data$end_station_name[combined_bike_data$end_station_name == ""] <- NA
combined_bike_data$end_station_id[combined_bike_data$end_station_id == ""] <- NA
```
- **Adding columns**

**1-** **Date, Month, Day, and year**

  Adding column for date, month, day, and year by using as.`Date()` on started_at to get the `date` and then getting the rest of the new columns.
```{r}
combined_bike_data$date <- as.Date(combined_bike_data$started_at)
combined_bike_data$month <-  format(as.Date(combined_bike_data$date), "%m")
combined_bike_data$day <- format(as.Date(combined_bike_data$date), "%d")
combined_bike_data$year <- format(as.Date(combined_bike_data$date), "%Y")
```
**2-** **Ride Length**

First im going to identify the correct format of `started_at` and `ended_at`
```{r}
unique(combined_bike_data$started_at)
```
As you can see that the format is YYY-MM-DD HH:MM:SS.
changing the data type in `started_at` and `ended_at` to the right format.
```{r}
combined_bike_data$started_at <- as.POSIXct(combined_bike_data$started_at, format = "%Y-%m-%d %H:%M:%S")
combined_bike_data$ended_at <- as.POSIXct(combined_bike_data$ended_at, format = "%Y-%m-%d %H:%M:%S")
``` 
Adding a column for ride length by deducting ended_at from started_at
```{r}
combined_bike_data <- combined_bike_data %>% mutate(ride_length = ended_at - started_at)
```
dividing the ride_length by 60 to represent it by minutes not seconds.
```{r}
combined_bike_data$ride_length <- as.numeric(combined_bike_data$ride_length)
combined_bike_data$ride_length <- as.numeric(combined_bike_data$ride_length/60)
```

**3-** **Days of The Week**
  
Adding column day_of_week to know the days of the week that the rides started_at.
```{r}
combined_bike_data <- combined_bike_data %>%
  mutate(day_of_week = weekdays(as.Date(combined_bike_data$started_at)))
```

- **Removing Columns**

Removing data that will not be useful to the analysis so i removed `start_lat`, `start_lng`, `end_lat` and `end_lng`.
```{r}
combined_bike_data <- combined_bike_data %>%
  select(-c(start_lat, start_lng, end_lat, end_lng))
```
- **Adding N/A to specific cells**  

Some cells need to be removed so i will add NA to them based on some specific conditions.

First `ride_length` it has some values that are equal to `0` and negative number that cannot be displayed  which means that the rider hasn't used the bike for any hours ,minutes or seconds and it is wrong to have the ride length in negative as well so i will not be using these cells in my analysis.

Then using `sum(is.na)` to know how many ride_length where equal to 0 and negative numbers, as you can see we had {1560} entry.
```{r}
combined_bike_data$ride_length[combined_bike_data$ride_length <= "0"] <- NA
sum(is.na(combined_bike_data$ride_length))
```
Then i will subset the data to do further adjustments to the data 
```{r}
subset_data <- combined_bike_data[!is.na(combined_bike_data$ride_length), ]
```
Next adding NA to values that are more than one day because we have some values in ride_length that are more than 12 thousand minutes which is not normal so any values that is more than one day will be replaced by NA.

ride_length that is more than 1440 minutes = one day.

Using sum(is.na) i found that the values that are more than 1440 minutes are 6882 entry.

```{r}
subset_data[subset_data$ride_length > 1440, "ride_length"] <- NA
sum(is.na(subset_data$ride_length))
```
combining the subsets back to the original data.
and using sum(is.na) the total N/A's in the ride_length of the combined_bike_data is 8442 entry.
```{r}
combined_bike_data[!is.na(combined_bike_data$ride_length), ] <- subset_data
sum(is.na(combined_bike_data$ride_length))
```
Dropping the subset data.  
```{r}
rm(subset_data)
```
- **Deleting N/A Values**

Using `na.omit()` to delete the `N/A` values in the data frame.
```{r}
combined_bike_data <- na.omit(combined_bike_data)
```
There were {1,375,436} observations deleted and now the available observations that i will use for analysis is {4,331,652} observation,
mostly the deleted N/A's were from the `start_station_name`, `end_station_name`, `start_station_id`, `end_station_id` and some rows in the `ride_length`.
 
# Analyze

#### Ride Length
---------------

- Calculating the mean, min, and max for the ride length

mean is {15.9} minutes , min is {0.016} and max is {1439.8} minutes.

```{r}
mean(combined_bike_data$ride_length) 
min(combined_bike_data$ride_length)
max(combined_bike_data$ride_length)
```
#### Ride Length and Rideable Type
---------------------------------

- Doing a summary to see what type of ride is being used more when the ride length is more than one hour and less than one hour.

First condition is using the electric and classic bikes when the length is more than 60 minutes. 
  
the outcome is that {298,193} rides were electric bikes and {494,360} rides were classic bikes.

Second condition is using the same rideable types when the ride length is shorter than 60 minutes.
the outcome is that {1,234,413} rides were by electric bikes and {2,232,262} were by classic bikes.
Conclusion is that the classic bikes are being used more when the ride length is less or more than 60 minutes.
```{r}
sum(combined_bike_data$rideable_type == "electric_bike" & combined_bike_data$ride_length > "60")
sum(combined_bike_data$rideable_type == "classic_bike" & combined_bike_data$ride_length > "60")
sum(combined_bike_data$rideable_type == "electric_bike" & combined_bike_data$ride_length < "60")
sum(combined_bike_data$rideable_type == "classic_bike" & combined_bike_data$ride_length < "60")
```
- By using sum you can see that the total electric bike used from the total rides, {1,532,620} rides were by electric bikes and {2,726,663} rides were by classic bike so the classic bikes are being used more.
- 
```{r}
sum(combined_bike_data$rideable_type == "electric_bike")
sum(combined_bike_data$rideable_type == "classic_bike")
```
#### Member and Casual Riders Count
------------------------------------

- Using sum to know how many members do we have and how many casual riders do we have throughout the past 12 month.
we have {2,806,688} members and {1,524,848} are casual riders.

```{r}
sum(combined_bike_data$member_casual == "member")
sum(combined_bike_data$member_casual == "casual")
```
#### Member and Casual Riders / Electric and Classic bikes
---------------------------------------------------------

- Using sum to see who uses electric bike and classic bike more, is it members or is it casual riders?

First, member use more electric bike than the casual riders as {961, 489} rides were by members and {571,131} rides were by casual riders.

Second, members also use more classic bikes than casual riders as {1,845,199} rides were by members and {881,464} rides were by casual riders.

Casual riders and members tend to use classic bikes more than electric bikes.

```{r}
sum(combined_bike_data$rideable_type == "electric_bike" & combined_bike_data$member_casual == "member")
sum(combined_bike_data$rideable_type == "electric_bike" & combined_bike_data$member_casual == "casual")
sum(combined_bike_data$rideable_type == "classic_bike" & combined_bike_data$member_casual == "member")
sum(combined_bike_data$rideable_type == "classic_bike" & combined_bike_data$member_casual == "casual")
```
# Share

### Comparing Members Vs Casual Riders

- #### Day of Week


Using the `combined_bike_data` data frame we can see that members ride bikes the most on Thursdays and casual riders ride bikes more on Saturdays, this shows that there is a difference in the riding behavior between members and casual riders
```{r}
member_casual_color <- c (
"casual" = "brown",
"member" = "black"
)
combined_bike_data %>%
  group_by(member_casual, day_of_week) %>%
  summarise(count = n()) %>%
  ggplot(aes(x = day_of_week, y = count, fill = member_casual)) +
  geom_col(position = "dodge") +
  scale_fill_manual(values = member_casual_color, c("Member_Casual"))+
  labs(x = "Day Of Week", y = "Member-Casual Count", title = "Member vs Casual Riders By Week Days")+
  theme_minimal()
```
![Day Of Week](https://github.com/Karemelshimi/Cyclistic_Case_Study/assets/153403784/943cc5ad-de11-4921-8671-7913a472c084)

- #### Month

Using the same inputs from the last visualization but changing the `group_by` to group it by `month`.

The visualization shows tha for `members` August has the highest member riders for the past 12 month and `Casual` riders are riding bikes the most in July, overall June, July, and august are the highest 3 month of the past 12 that have the highest riders members and casual riders. 

The lowest month with riders are December, January, February, and March, they have the lowest riders for the past 12 month, this means that riders either members or casual riders usually ride the most in summer and ride fewer in the winter.
```{r}
combined_bike_data %>%
  group_by(month, member_casual) %>%
  summarise(count = n()) %>%
  ggplot(aes(x = month, y = count, fill = member_casual)) +
  geom_col(position = "dodge") +
  scale_fill_manual(values = member_casual_color,c("Member_Casual"))+
  labs(x = "Month", y = "Member-Casual Count", title = "Member Vs Casual Riders By Month")+
  theme_minimal()
```
![Month](https://github.com/Karemelshimi/Cyclistic_Case_Study/assets/153403784/5d679bad-bd0e-4234-8071-5f90d7756d0c)
### Comparing Classic Bike vs Electric Bike

This visualization shows comparison between classic bikes and electric bikes based on usage by casual riders and members.

Classic bikes are being used more by both members and casual riders but the overall usage of bike is more by members.

```{r}
combined_bike_data %>%
  filter(rideable_type %in% c("classic_bike", "electric_bike")) %>%
  group_by(rideable_type, member_casual) %>%
  summarise(count = n()) %>%
  ggplot(aes(x = rideable_type, y = count, fill = member_casual))+
  geom_col(position = "dodge") +
  scale_fill_manual(values = member_casual_color, c("Member_Casual")) + 
  labs(x = "Rideable Type", y = "Member-Casual Count", title = "Classic Bike Vs Electric Bike") +
  theme_minimal()
```
![Classic Bike Vs Electric Bike](https://github.com/Karemelshimi/Cyclistic_Case_Study/assets/153403784/ce1cdb83-1c50-436a-9329-72b67d3dbc00)
### AVG Ride Length
Comparing members vs casual riders based ob=n their ride length to see what are their riding habits. 

This visualization shows that the casual riders tend to spend more time riding as their avg rides exceed 22.5 minutes, members spend much less time as their avg rides length is shorter than 12.5 minutes.

```{r}
combined_bike_data %>%
  group_by(member_casual) %>%
  summarise(avg_ride_length = mean(ride_length, na.rm = TRUE)) %>%
  ggplot(aes(x = member_casual, y = avg_ride_length, fill = member_casual)) +
  geom_col(position = "dodge") +
  scale_fill_manual(values = member_casual_color, c("Member_Casual"))+
  labs(x = "Member_Casual", y = "Ride Length", title = "Member vs Casual Riders By Ride Length")+
  theme_minimal()
```
![AVG Ride Length](https://github.com/Karemelshimi/Cyclistic_Case_Study/assets/153403784/b181c881-bd45-44eb-8a95-15fa84ebb845)

