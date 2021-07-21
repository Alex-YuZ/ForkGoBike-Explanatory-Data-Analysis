# FordGoBike Data Exploration
## by Alex (Zhu Yu)
### Overview

The bicycle-sharing service is becoming more and more popular across the globe. It allows people in metropolitan areas to rent bicycles by unlocking in one station and return in any other authorized network station. This is very convenient for short trips, especially ideal for one-way trips.

Ford GoBike, which was initiated in 2013, is one of them that was introduced to the US West Coast. The bikes are available for use 24 hours per day, 7 days per week, 365 days in a whole year. Up until now, it has facilitated hundreds of thousands of people and collected millions of usage data since 2017.

## Introduction to the Dataset

The dataset for this project covers **23** months of data, ranging between **2017-06** to **2019-04**, with nearly **3 million** records.

### Data Source

The data are originally stored in <a href='https://s3.amazonaws.com/fordgobike-data/'>s3.amazonaws.com</a>. It consists of **17 csv-formatted** files (___Notice: originally compressed as `.zip` file___) and named after `year + -fordgobike-tripdata.csv` or `year-month + -fordgobike-tripdata.csv`. Before analysis and visualization, I executed some data wrangling work.

### Data Wrangling

The data wrangling process went through three main steps:

> **I. Data Gathering.**  
> **II. Data Assessing.**  
> **III. Data Cleaning.**

**I. Data Gathering.** I downloaded these 23 files from <a href='https://s3.amazonaws.com/fordgobike-data/'>s3.amazonaws.com</a> programmatically. Then I used `zipfile` packages to unzip them all to `.csv` format. Finally I merged all these single files as ___one uniformed dataset___.

**II. Data Assessing.** I conducted several evaluation procudures such as ___Feature Consistency Check___, ___Data Type Check___, ___Missing Value Evalution___, ___Duplicated Value Check___ etc. The following data quality issues has been revealed:

1. Columns have missing values：
> - `start_station_id` - ___nullity: 0.38%___, 
> - ` start_station_name` - ___nullity: 0.38%___, 
> - `end_station_id` - ___nullity: 0.38%___, 
> - `end_station_name` - ___nullity: 0.38%___, 
> - `member_birth_year` - ___nullity: 6.7%___, 
> - `member_gender` - ___nullity: 6.7%___, 
> - `bike_share_for_all_trip` - ___nullity: 15.97%___.   
2. `member_birth_year` could be `int` instead of `float64`.
3. `member_gender`, `bike_share_for_all_trip` could be `CategoricalDtype` instead of `object` (string).
4. `start_station_id`, `end_station_id`, `bike_id` could be `object` (string) instead of `float64` or `int`.
5. `start_time`, `end_time` could be `timestamps` data type instead of `object` (string). 
6. Some new features need to be derived: 
> - `member_age` from `member_birth_year`
> - `start_month` from `start_time`
> - `start_date` from `start_time`
> - `start_day_of_week` from `start_time` (See ___Remark 1___.)
> - `start_hour` from `start_time`
> - `trip_distance` from `start_station_latitude`, `start_station_longitude`, `end_station_latitude`, `end_station_longitude` ((See ___Remark 2___.))

> > ___Remark 1:___ After `start_day_of_week` is created, we can order them in real world by using `pd.api.types.CategoricalDtype`.  
> > ___Remark 2:___ This can be achieved by using `distance()` function from `geopy.distance` package.
7. Abnormal value in `member_birth_year` with **1878**.

**III. Data Cleaning.** The data quality issues found in the **Data Assessing** process have all been solved one by one and recorded in detail in the jupyter notebook:

1. Dealing with Missing Values.
2. Conventional Data Type Conversion.
3. `datetime` Data Type Conversion.
4. Feature Extraction including ___Time-Based Feature Extraction___ and ___Distance-Based Feature Extraction___.
5. Dealing with Outliers or anomaly points in distance-related feature and age-related features.
6. As auxiliary feature for analysis, I created `age_range` feature by cutting age values in `member_age` into several bins.
7. Finally, drop those features which would be not helpful for this project.

## Summary of Findings

### **Three** main explorations have been conducted:

- **Univariate Exploration**
- **Bivariate Exploration**
- **Multi-variate Exploration**

### The following **features** have been explored:

**Numeric Features**
- > `duration_min`
- > `trip_distance_km`
- > `member_age`

**Categorical Features**
- > `user_type`
- > `member_gender`
- > `bike_share_for_all_trip`
- > `start_time_year_month`
- > `start_time_month`
- > `start_time_date`
- > `start_time_dow`
- > `start_time_hour`
- > `age_range`

### 1. Univariate Exploration
- For `duration_min`, **99%** of the duration values or values less than **64 minutes**, **Extreme values** can go up to as large as **1438 minutes** (< 1%); Half of the duration time for a ride fall bewteen **5.75 mins (Q1) and 13.9 mins (Q3)** with average value being **12.9 mins**; When scaled in log , it exhibits a normal distribution.

- For `trip_distance_km`,  when scaled in log, it also presents a **bell-like shape** which implies it has a long tail on both ends; Almost **99.99%** of data is within **9km**, and **1km and 2km** are the most common ones.

- For `member_age`, the average for all users is ___33 y/o___, with ___minimum age 18___ and ___maximum age 66___; The distribution in the histogram is right-skewed with a longer tail up to 66 years-old; Most of the users' ages range between ___27 and 42___, while ages at two ends e.g. **10-20** and **60+** have the least rentals.

- For `member_gender`, there are three types of gender: **Males**, **Female** and **Other**. **Male** users play a dominant role, almost as **3 times** the number as that of **Female** users.
Users in **Other** gender have the least numbers, only covering **1.6%** of the total.

- For `user_type`, there are two types - `Customer` and `Subscriber`, among which `Subscriber` covers the most, nearly as **8** times as that of **customers**. The reason for this maybe because being a subscriber can bring more benefits such as less pricing, additional coupons, better service etc.

- For `bike_share_for_all_trip` program, the number of people who choose it is far less than those who don't unexpectedly.

- In terms of time series, the total number of rentals mounts up to **220 thousand**, almost **100 times** larger than that in the beginning. In detail,  
  - The curve bewteen **year-month** and **number of rentals** shows **seasonal pattern** which means more rentals happen during spring and summer, but obvious decreases are always seen in **winter** or between **October** and **December**. 

  - On ___days in a week___ scale, the bikes are  more popular on weekdays than that at the weekends as a clear descent is seen. Numerically, the number of rentals at the weekend is ___only half___ of that on weekdays. 
  
  - In different ___hours of a day___, periods such as **7:00 - 9:00 in the morning** and **16:00 - 19:00 in the evening**, a.k.a. the ___rush hours___, see more rentals than the others which may imply that the dominant users for bike rentals are from **working class**.

### 2. Bivariate Exploration

- As the majority of users is **Subscribers**, so when the population is divided by `user_type`, we can see that the general trend for relationship between number of rentals and year-month in **Subscribers** is very similar to the trend we observed in the ***univariate exploration part***. It also presents a **seasonal pattern** when winter comes in and temperature is low, the rentals are going down, while in pleasing climate like spring and summer with cozy weather conditions, the number of rentals are up. However, users with **Customer** type do not present so much strong pattern as **Subscribers**.

- When taking **genders** and **bike share for all trip program** into consideration, it is clear that **Males** and **those who do not** take part in the **program** lead the number of rentals with time and also exhibit a similar **seasonal trend** to the total rentals as a whole, this is because **Males** and ___non-member___ of the **bike share for all trip program** is the majority of the whole user population.

- When considering the age of users, the rentals in **(10, 20]** and **(60, inf)** groups do not show much change with time, while the others show again the **seasonal trend** to some extent, i.e. for groups of **(20, 30], (30, 40], (50, 60] y/o**, this pattern is obvious and strong especially in users within **(20, 30] and (30, 40] y/o** range.

- For the distribution of duration time and trip distance per ride throughout days of a week, it seems that the median and interquartiles for the **trip distance per ride** is almost **the same**, and only the median and interquartile for the **duration time** are showing slight differences at the weekends (**larger and more varied**) than that on weekdays. 

### 3. Multi-variate Exploration

- **Time** (months in a year, days in a week, hours in a day), **ages**, **user type** (`Customer` or `Subscriber`) seem to have little effect on the average duration time and trip distance for a ride but **gender** type. **Males** always tend to have the shortest values in terms of avg. duration and distance.

- Users in different **user type** have different bike rental patterns at the weekends compared with that on weekdays. 

  - From **Monday to Friday**, rentals mostly happen during commute hours which are **7:00 - 9:00 in the morning** and **16:00 - 18:00** in the afternoon, no matter what kind of user type they belong to.

  - On **Saturday and Sunday**, the difference between these two user types occurs: **Customer users** tend to rent bikes during weekends more often than they do on weekdays, especially during the period **from 10:00 in the morning to 16:00 in the afternoon**. However, **Subscriber users** seem to have disappeared during that period at the weekends, maybe they prefer taking a tour without bicycles or just relaxing at home.

- Adults like 20 - 40 y/o and middle-aged users like 50 - 60 y/o have very close average duration time and trip distance values and present a similar pattern throughout days in a week.

- Surprisingly, for the **10 - 20 y/o** group, no matter it's scaled with other categorical features like **gender** or **user type**, their average values generally showed a gap from other age groups and are distributed more dispersedly from the others.

## Key Insights for Presentation

The following explorations are extracted for presentation:

**In univariate exploration:**
-  With respect to `duration_min`, it shows normal distribution on log-scale histogram, with average value being 12.9min; 95% of the data fall within 27.3 minuets and 99% of the data fall within 63.95 minutes.
-  With respect to `member_age`, it shows right-skewed distribution on the histogram; the interquartile range is (27, 42] y/o; ages between 10-20 and 60+ have the least numbers.
-  With respect to `member_gender`, there are three types of genders. **Male users** play a dominant role, **Female users** cover less than 1/4 of the total.
- With respect to `user_type`, there are two user types. The number of **Subscriber** is almost as 8 times as that of **Customer** user type.
- With respect to the `year-month` time scale, The general trend for the bike rentals is increasing over the years. There seems to be a seasonal pattern in the trend, that all the increases concentrate when the climate is warm and the peaks arrive at around ___October___ but it begins to decline as winter comes in.
- With respect to the `days in a week` time scale, bike rentals on weekdays are far more than that at the weekends, almost 2 times as large, especially from **Tuesday** to **Thursday**.
- With respect to the `hours in a day` time scale, peak rentals occur during rush hours, i.e. **7:00 - 9:00 in the morning** and **16:00 - 19:00 in the evening**.

**In bivariate exploration:**
- The users with **Subscriber** type present similar trend to the general trend we found earlier in univariate exploration : the **seasonal trend**, while for **Customer** user type, it does not show much change with time.
- **(10, 20] group** and **(60, inf) group** do not show much change with time, while the other groups show again the **seasonal trend** to some extent, i.e. for groups of **(20, 30], (30, 40], (50, 60]**, this pattern is obvious and strong especially in users with age range of **(20, 30] and (30, 40] y/o**.


**In multi-variate exploration:**
- **Time** (months in a year, days in a week, hours in a day), **ages**, **user type** (`Customer` or `Subscriber`) seem to have little effect on the average duration time and trip distance for a ride but **gender** type. **Males** always tend to have the shortest values in terms of avg. duration and distance.
- Users in different **user type** have different bike rental patterns at the weekends compared with that on weekdays. Both **Customer** and **Subscriber** type of users are more likely to rent bikes at similar periods of a day through Monday to Friday. However, the difference occurs at the weekends. **Customer** users are more active **from 10:00 in the morning to 16:00 in the afternoon** at the weekends while **Subscriber** users seem to have disappeared during that period at the weekends, maybe they prefer taking a tour without bicycles or just relaxing at home.


##  Environment and Package Requirements
This project is ran on **Python 3.7.9** and ___dependent on___ the following **packages**:

- pandas 1.1.3
- numpy 1.19.2
- matplotlib 3.3.2
- seaborn 0.11.0
- missingno 0.4.2
- requests 2.24.0
- geopy 2.1.0

## References

- Visualize missing data using <a href='https://github.com/ResidentMario/missingno'>missingno</a> package.
- Calculate distance between coordinates using <a href='https://geopy.readthedocs.io/en/stable/#'>Geopy</a> package.
- <a href='https://www.roadbikerider.com/whats-the-average-speed-of-a-beginner-cyclist/#:~:text=Many%20beginning%20road%20cyclists%20ride,18%20mph%20or%20even%20higher.'>Average speed</a> for a bicyclist.
- What is the <a href="https://www.lyft.com/bikes/bay-wheels/pricing">difference</a> within customer, subscriber and bike-share-for-all-trip.
- <a href='https://stackoverflow.com/questions/39500265/manually-add-legend-items-python-matplotlib'>How to Manually add legend Items Python matplotlib</a>