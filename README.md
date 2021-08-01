


# FINAL PROJECT
## BIKESHARING SERVICES
An Analysis of Public Bike Sharing Services in Washington DC to uncover growing membership trends for potential business expansion

## REASONS FOR SELECTING TOPIC
We want to offer a deeper analysis to investors and the community on the growing membership trends of Public Bike Sharing Services in Washington DC in order to motivate investors to expand the availability of bike stations 

The primary incentive to investors in the bike sharing market is the significant revenue gained from an economic model which includes:
1. Data mining -- marketing and analysis of users' riding behavior data captured by the app used to locate and unlock the bikes and GPS complement data sets collected by taxi and public transportation systems.
2. Advertising -- high commercial value to retail businesses, restaurants to place ads on the physical bikes and on the apps used to locate and unlock the bikes.
3. Interest on deposits -- Interest earned from memberships is a huge income generating asset to invest.

A 2021 bike sharing market growth, trend, and forecast report by market intelligence and advisory firm Mordor Intelligence. 

![image](Image/bps_forecast.png) 

## VARIABLES TO CONSIDER
- Location of Bike Station (Latitude, Longitude, and Zip code)
- User types: Member and Casual
- Bike routes: Popular and Unpopular
- User number: Weekday and Weekend
- Time of day of the ride

## DESCRIPTION OF THE SOURCE DATA
For our analysis, we are using secondary data which has already been collected by the capital bike share website (https://www.capitalbikeshare.com/system-data). The data was stored in CSV format and we were able to find data from 2010 to 2021. 

For the years 2010 and 2011 the daily bike ride information of the whole year was gathered in a unique CSV file. From 2012 to 2017, the daily bike ride information was gathered in quarterly arranged CSV files and from 2018 to 2021 the bike ride information was collected in monthly CSV files. Data for the month of april 2020 was not available and year 2021 cut in the month of may.

In total, our final data was 66 CSV files with over 26,000,000 rows and 8 columns each.

### BikeSharing Data:
- Capital Bikshare (Washington D.C.) from 2010 - 05/2021. Retrieved from: https://s3.amazonaws.com/capitalbikeshare-data/index.html

## QUESTIONS TO ANSWER
Q. How does time of day influences the member type of riders in Washington D.C.?

Q. How does the weekday/weekend difference influence the membership type of riders in Washington D.C.?

Q. How does type of bike affect membership type?

H0: The member type of riders is not related to the time of the day of the ride. 

## ETL Process

### Table1_all_bike_trips
1. Process Capital Bikeshare bike trip data sets to create one big dataset that contains all 28M trips with followings columns: 'Trip Number,' 'Starts station number,' 'End station number,' 'start date,' 'end date,' and 'member type' to enable us review when and where each bike trip took place and whether it was done by a member or a casual user. 
    - Downloaded the total 65 Capital Bikeshare bike trip dataset available from `Index of bucket "capitalbikeshare-data"` page of Capital Bikeshare website.
    - Create an index of the 65 files as a csv file to allow python code able to call files by each file's directory and file name. Then, process files with index 0-52 and 53-65 separately as those files have different columns. The list can be refered to the csv file <Resources/capitalbikeshare_dataset_index.csv>.
   - The files from index 0-52 have unique column 'Bike number' which define which bike was used for each trip.
   - After cleaning the data and dropping unnecessary columns, output the dataframe as csv file `trips_2010to202003.csv`.
   - The files with indexes 53-65 have unique column called 'rideable_type,' which describes which bike type was used. We extract this information separately to analyze how this variable affects the membership type of users.
   - After cleaning the data and dropping unnecessary columns, output the dataframe as csv file `trips_202005to202105.csv`.
   - Merge the `trips_2010to202003.csv` and `trips_202005to202105.csv` files together 
   - Drop unnecessary columns, clean up NaN rows, remove mixed data type columns, convert datatype, and output the dataframe as `Table1_all_bike_trips.csv`.
 
### Table2_bike_number and Table3_rideable_type
2. Create two additional tables from `trips_2010to202003.csv` and `trips_202005to202105.csv` that represent 'Bike number' and 'rideable_type' of bikes respectively.
   - Extract `Table2_bike_number.csv` dataframe and ouput as csv from the `trips_2010to202003.csv`.
   - Extract `Table3_rideable_type` dataframe and ouput as csv from the `trips_202005to202105.csv`.

### Table4_station_list and Table5_station_active_dates
3. Create a dataset with list of `station_id` and corresponding latitude and longitude by utilizing the datasets from April 2020 to May 2021. Note the most recent file available is May 2021 and Capital Bikeshare started to record geographic information since April 2020.
    - Use the files from index 53 to 65 to create a new dataframe with 'stationnumber' and its corresponding geocode.
    - The output csv file `Table4_station_list` has columns `stationnumber`, `lat`, and `lng` to refer the location of each bike station.
  
4. Output an additional list of bike stations that displays the first day and the last day of their usage to identify if any stations have imbalance in number of data points due its length of operation.
    - Use the `Table1_all_bike_trips.csv` and pick the first time and the last time that each `startstationnumber` appears in the list
    - Output the dataframe with `startstationnumber`, `first`, and `last` as a csv file `Table5_station_active_dates.csv`.

### Table6_number_of_trips
5.  From the `all_bike_trips.csv` file, extract an additional dataframe that displays number of trips took by each staion number.
    - Utilize `value_counts()` method and list how many times does each station appear over the 28M trips.
    - Output the dataframe with `startstationnumber` and its `occurence` as a csv file `Table6_number_of_trips.csv`.

### Table7_member_trips and Table8_casual_trips
7.  Create two separate datasets listing trips done by 'Member' users and 'Casual' users separately.
    - Refer to the `Table1_all_bike_trips.csv` and pick the rows with `membertype` being `Member` and `Casual` separately into two dataframes: `Table7_member_trips.csv` and `Table8_casual_trips.csv`.

### Table9_ratio_df, Table10_dayofweek_ratio, and Table11_station_dayofweek_ratio
8.  Create a table listing the ratio of trips done by 'Member' users over the all trips have done at each station.
    - Refer to the `Table7_member_trips.csv` and `Table8_casual_trips.csv` files for number of trips done at each station by `Member` and `Casual` using `value_counts()`. Then, calculate the ratio based on those values to figure out the ratio of `Member` usage by each station.
    - Output the resulted dataframe as `Table9_ratio_df.csv` file.
    
9.  Create a table listing the ratio of trips done by 'Member' users over the all trips by each day of week.
    - Refer to the `Table7_member_trips.csv` and `Table8_casual_trips.csv` files and figure out the ratio of `Member` users by each day of week.
    - Output the resulted dataframe as `Table10_dayofweek_ratio.csv` file.

10. Create a table listing the bike stations and their ratio of 'Member' usage for each day of week.
    - Refer to the `Table7_member_trips.csv` and `Table8_casual_trips.csv` files and calculate the number of `Member` usages and `Casual` usages for each bike station on each day of week.
    - Utilize the calculated dataframes and output a new dataframe with columns of `startsstationnumber`, `weekday`, and the `ratio` of `Member` usage as a csv file `Table11_station_dayofweek_ratio.csv`.

Table #1 all_bikes_trips

![image](https://user-images.githubusercontent.com/78698456/125867516-cb3773d8-bef6-4fa0-bc48-196176f0c837.png)

Table #2 bike_number

![image](Image/2_bike_number_table.png)

Table #3 Rideable_type

![image](Image/3_rideable_type_table.png)

Table #4 Station List

![image](Image/4_station_list_table.png)

Table #5 Station List with its active dates

![image](Image/5_station_activedates_table.png)


## Database Integration

Our database has been refined and currently, holds the following schema:

![Final_ERD](https://user-images.githubusercontent.com/78656720/126044898-bf43e200-e51a-45a5-83e4-865fc1d466df.png)

The connection between tables, the fields that have been kept and the data types are displayed in the image above. After cleaned and organized the raw data extracted from the citibike website, we created five tables that are ready for our analysis and load the in to PostgerSQL database. While we are working on our project we have also extracted different tables using SQL join query. Looking at the image below we can see the Table created by Inner join from our *station_list* and *station_list_active*.

![Inner_Join](https://user-images.githubusercontent.com/78656720/126045190-2021a9e1-b07f-40b8-9c1a-0a5cddf2b1ea.png)

## MACHINE LEARNING
In the machine learning part of our project, we try to predict future growth of bikesharing in DC. Using timeseries data and ARIMA model the project performs trend analysis and predict future prospect of the bike sharing.
This project tries to achive two main goals in this time series analysis. First, it identifies the sequence of observations , and second, predict the future values of the the timeseries univeriate variable(**Number of Trips**).

 - **Visualizing time series**
 Looking at below chart we can see the upward trend of *Number of trips* from 2010 till 2018.
 
![Bike_Trip_timeseries](https://user-images.githubusercontent.com/78656720/126903853-9a9e3504-207f-4bb7-98a9-63f2cbdba308.PNG)

Regarding timeseries analysis our series needs to be stationary (i.e *Number of trips* should have a constant mean, variance and covariance). However, as we can see the above chart,the mean is not constant.This implies our series is not stationary.

![Scatter_Biketrip](https://user-images.githubusercontent.com/78656720/126904090-a0e5aa5b-5212-46de-9230-d60973eeb122.PNG)

We can also visualize the normal distribution of our timeseries data using a probablity distribution.


![normal_distribution](https://user-images.githubusercontent.com/78656720/126906969-70494e4e-1d44-40a1-9839-866bbac21744.png)


To get a more clear insight of our timeseries data, we will plot the **level**, **Trend**, **Seasonality**, and **Noise** charts.These charts explains the average value in the sseries, the increasing or decreasing value in the series, the repeating short-term cycle in the series, and the random variation in the series respectively.

![Trend_Seaasonality](https://user-images.githubusercontent.com/78656720/126904291-004881c1-7a03-4cd2-b39e-cdad2fad1b88.PNG)

Looking at the above charts there is an upward trend and a recurring event where number of bike trips shoots maximum every year.

  - **Stationarising the time series**

 First, we check if our series is stationary or not. We use ADF(Augmented Dickey-Fuller) Test, as it can be used to determine the presennce of unit root in the series and also determine if the series is stationary or not.
 *Null Hypothesis(H0)*: The series has a unit root
 *Alternative Hypothesis(H1)*: The series has no unit root.
 
 ![RMSE](https://user-images.githubusercontent.com/78656720/126905072-b7d43c22-ed06-4c2f-ae24-1ffcab11a250.PNG)

As we can see the above image the p-value is greater than 0.05. Thus, we fail to regect our null hypothesis.We can coclude ourseries is not stationary. To get a stationary series, we need to eliminate the trend and seasonality from the series.  we do that by taking a log of the series to reduce the magintude of the values and reduce the rising trend in the series. Then,we find the rolloing average of the series. 

![Moving_average](https://user-images.githubusercontent.com/78656720/126905348-1106eb4e-98e3-47fe-ab7c-e79cc0aed6db.PNG)

After finding the mean, we take the difference of the series and the mean at everypoint in the series.Therfore, we eliminate trends out of a series and obtain a more stationary series. Now, we can perform the Dickey-Fuller test(ADFT) once again to check if our series attained stationarity.

Using the same rules of P-value we can clearly see our series attained stationarity.

![Log_movingaverage](https://user-images.githubusercontent.com/78656720/126905518-3bfe4056-105c-4d25-b7d6-e3764073b324.PNG)


## PROPHET MODEL:

We chose to use the Facebook Prophet model because it is also a time series forecasting algorithm and because it easily handles the weekly and yearly seasonality contained in our data. We used the Prophet model to predict 'Member' rider growth for the next 36 months.


### Preprocessing

The 'membertype' target column was encoded for the 'member' and 'casual' riders from strings to numerical data for the algorithm to understand the two categories. Then the 'casual' riders were dropped, and the 'startdate' column was converted from an object to a datatime data type. The columns were then renamed because Prophet requires the use of specific column names; 'ds' for dates and 'y' for the target variable we're predicting.

![image](Image/prophet_preprocessing1.png)

The 22 million datapoints of our dataframe were too many to run on any of Google Colab's free server runtime types, so we binned the observations from a daily, minute-by-minute tally to weekly summed totals. This reduced the dataframe to a more manageable 486 datapoints.


![image](Image/prophet_preprocessing2.png)

### Train and Fit the Model

Since the last two years were atypical, due to COVID19 restrictions, and didn't represent the previous years' rider behavior, we limited our model to using the rider data from 2010 - 2019. 

We created the train dataset with datapoints from 2010-2017 and test dataset to 2018-2019.

![image](Image/prophet_train_test.png)

### Forecast and Plot

When making predictions with Prophet, we're required to create a special object called 'future_data' which is a Pandas DataFrame with a single column (ds) that includes all datetimes within the training data, plus the additional periods from the 'test' split. Our time series has a clear yearly cycle with summer months higher member rides and tapering off in the winter months.

![image](Image/prophet_weekly_forecast.png)


### Trend and Seasonality

Prophet also returns components of our forecasts which show how weekly and yearly patterns contribute to the forecasted values. The first plot shows that member weekly rides has been gradually increasing over time. Although the overall growth trend has been gradual, it has risen steadily year over year. The weekly trend shows higher weekend usage suggesting that members may not be using bikes for commuting or errands and more for recreation. The seasonal yearly trend shows peak member usage in the spring and fall seasons following Washington, DC's most moderate weather temperatures during those times.


![image](Image/prophet_weekly_trend.png)



## DASHBOARD
The interactive Tableau Dashboard is available at the following link: <https://public.tableau.com/views/PublicBikeSharingProjectLoationvs_Membership/Loc_Station_ratio?:language=en-US&publish=yes&:display_count=n&:origin=viz_share_link>.

Below are the explanations and screenshots of key visualizations.

### Visualization of Trip number change overtime by Member or Casual users

Observations:
1. The majority of trips have been taken by 'Member' users. 
2. The trend of increase in 'Member' user ratio is sustained until 2019
3. In general Q1 and Q4 experience lower bike trips overall

![image](Image/Membershi_Trip_overtime.png)

### Visualization of Station and Member or Casual Users

Observations:
1. We see higher ratio of 'Casual' users at the stations located near Point of interest suchs as Lincoln Memorial, Museums, White House, etc...
2. The 'Member' users are often starting their travel near Union Station, Dupont Circle, Mass Ave, Rhode Island Ave, Pennsylvania Ave, etc...

![image](Image/Membership_by_station.png)

### Visualizations of 'Member' user Ratio by Weekday

Observations:
1. The ratio of trips done by 'Member' users in weekday is 80-85%
2. The ratio of trips done by 'Member' users in weekday is 64-65%

![image](Image/Membership_Ratio_byDayofWeek.png)

Future visualization ideas:

1. Evaluate how number of trips changes by staion depends on Weekday or Weekend
2. Analyze how time of day may affect the overall trip occurence and the ratio of 'Member' user.
