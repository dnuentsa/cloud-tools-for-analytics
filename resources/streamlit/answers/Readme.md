
# Load and Transform the Data Set
## Get  the dataset 
- Download the zip file named Session-04-car_sharing_data_for_Streamlit.zip from Campus, 
- Unzip it and save all the csv files in the **datasets/** folder of your repository folder

## Create a new Page in your Streamlit Code
Create a folder named pages/ inside your code repository and create a new file which will hold the code you will write in this lab. Name your file  `evaluation_dashboard.py`.
Open the file and import the pandas and streamlit libaries used  this lab
```python
import streamlit as st
import pandas as pd
```

## Load Data into Dataframes 
Write a python code to load **only** the cars, trips and cities files into separate dataframes.
You should  create a  function to load only those datasets  from csv files
The skeleton is given below 

```python
# Function to load CSV files into dataframes
@st.cache_data
def load_data():
    trips = pd.read_csv("data/trips.csv")
    cars = pd.read_csv("data/cars.csv")
    cities = pd.read_csv("data/cities.csv")

    return trips, cars, cities
```
## Join all the dataframes
Until here, you should have three dataframes for `trips`, `cars` and `cities`. 

The goal is to have all the `trips` with the relevant information from all other dataframes joined into the unique `trips` dataframe. For instance, the car details are not available in the `trips` dataframe. Only the car_id is available.
To better understand the relationship between the tables, you have to refer to the database schema at 
[Database Schema](https://github.com/dnuentsa/cloud-tools-for-analytics/blob/main/resources/postgresql/database_schema.png) 

- Start by merging   the contents of the `trips` dataframe 
 with `cars` dataframe using the `trips` id column and the   `car_id` column. Use the dataframe [Join()](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.join.html#pandas.DataFrame.join) function for this purpose.
- Store the results into a new dataframe called `trips_merged`
```python
# Merge trips with cars (joining on car_id)
trips_merged = trips.merge(cars, left_on="car_id", right_on="id", suffixes=("", "_car"))
```
Then continue by merging the new `trips_merged` dataframe with the `cities` dataframe
```python
# Merge with cities for car's city (joining on city_id)
trips_merged = trips_merged.merge(cities, left_on="city_id", right_on="city_id", suffixes=("", "_city"))
```
## Clean useless columns
Use the dataframe drop() function to drop all the *id* columns. They are not useful for our analysis and dashboard.
```python
trips_merged = trips_merged.drop(columns=["id_car", "city_id", "id_customer", "id"])
```
## Update the Trips Date Format 
Currently the trips date in pickup_time and dropoff_time column are not in date format. 
- Transform them into datetime using to_datetime()
Use the  pandas function to_datetime()
```python
df['pickup_date'] = pd.to_datetime(df['pickup_time']).dt.date
```
Create a new column called "pickup_date" and get the date from `pickup_time`.
Use the  pandas function to_datetime()
```python
df['pickup_date'] = pd.to_datetime(df['pickup_time']).dt.date
```

# Filtering and Metrics
## Provide Filters on a sidebar 
Create a sidebar where the user will be able to filter the merged dataframe by car brand. 

- Provide a component on the sidebar to let the user select or or multiple car models. You can use the streamlit function multiselect() for this purpose.
```python
cars_brand = st.sidebar.multiselect("Select the Car Brand", trips_merged["brand"].unique(),  trips_merged["brand"].unique())
```
Once the user select the car brand, use the result to filter the dataframe on the `brand` column. Use the dataframe `isin()` function
```python
trips_merged = trips_merged[trips_merged["brand"].isin(cars_brand)]
``` 
## Provide Business Metrics 
Now that you have your trips in one single merged dataframe, you shall compute some metrics to show how the business is performing
In each separate column, provide the following metrics : 

- The total number of trips: this shall be obvious.
- The car model that provided the most revenue. Use dataframe groupby() function, then sum() inside each group, then get the max entry (using idxmax())
- The Total Distance across all trips. Use the sum() function on the relevant column.

After getting the right metrics from the dataframe,  You shall use the streamlit function metric() 
```python
# Compute business performance metrics
total_trips = len(trips_merged)  # Total number of trips
total_distance = trips_merged["distance"].sum()  # Sum of all trip distances
# Car model with the highest revenue
top_car = trips_merged.groupby("model")["revenue"].sum().idxmax()
top_car_revenue = trips_merged.groupby("model")["revenue"].sum().max()

# Display metrics in columns
col1, col2, col3 = st.columns(3)
with col1:
    st.metric(label="Total Trips", value=total_trips)
with col2:
    st.metric(label="Top Car Model by Revenue", value=top_car, help=f"Total revenue: ${top_car_revenue:,.2f}")
with col3:
    st.metric(label="Total Distance (km)", value=f"{total_distance:,.2f}")
```
## Preview the contents of your dataframe
Use Streamlit write() function and the dataframe head() to display the first lines of your  dataframe `trips_merged` 

# Visualizing the data
In this section, you shall use your data visualization knowledge to show nice dashboards with the data stored in your dataframe. Provide at least three visualizations using streamlit functions line_chart(), bar_chart() or area_chart() among the following questions 

- Trips Over Time
- Revenue Per Car Model
- Cumulative Revenue Growth Over Time
- Number of Trips Per Car Model
- Average Trip Duration by city 
- Revenue by city

```python
# **Trips Over Time (Line Chart)**
st.subheader(" Number of Trips Over Time")
trips_per_day = trips_merged.groupby("pickup_date").size()
st.line_chart(trips_per_day)

# **Revenue Per Car Model (Bar Chart)**
st.subheader(" Total Revenue by Car Model")
revenue_per_model = trips_merged.groupby("model")["revenue"].sum()
st.bar_chart(revenue_per_model)

# **Cumulative Revenue Growth Over Time (Area Chart)**
st.subheader(" Cumulative Revenue Growth")
trips_merged["cumulative_revenue"] = trips_merged["revenue"].cumsum()
cumulative_revenue = trips_merged.groupby("pickup_date")["cumulative_revenue"].max()
st.area_chart(cumulative_revenue)


# **Trips Per Car Model (Bar Chart)**
st.subheader(" Number of Trips per Car Model")
trips_per_model = trips_merged["model"].value_counts()
st.bar_chart(trips_per_model)


# **Revenue Distribution by City (Bar Chart)**
st.subheader(" Revenue by City")
revenue_per_city = trips_merged.groupby("city_name")["revenue"].sum()
st.bar_chart(revenue_per_city)

# **Trip Duration Distribution (Bar Chart)**
st.subheader(" Trip Duration Distribution (Avg per City)")
trips_merged["trip_duration"] = (trips_merged["dropoff_time"] - trips_merged["pickup_time"]).dt.total_seconds() / 60  # Convert to minutes
avg_trip_duration_city = trips_merged.groupby("city_name")["trip_duration"].mean()
st.bar_chart(avg_trip_duration_city)
```





