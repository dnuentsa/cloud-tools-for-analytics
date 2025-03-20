
# Load and Transform the Data Set
## Get  the dataset 
- Download the zip file named Session-04-car_sharing_data_for_Streamlit.zip from Campus, 
- Unzip it and save all the csv files in your computer

## Upload the datasets in your code 
- Reuse the git repository we use during the class lab on streamlit. You shall already have that on github. 
- Open a codespaces on it
- Once it opens, upload the csv files in the **datasets/** folder. If the folder doesn't exist, create it
- Commit and Push your  work to github with the new datasets. Give a meaningful comment like *uploading car sharing data*

## Create a new Page in your Streamlit Code
Create a folder named pages/ inside your code repository and create a new file which will hold the code you will write in this lab. Name your file  `car_sharing_dashboard.py`.
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
 with `cars` dataframe using the `trips` id column and the   `car_id` column. Use the dataframe [merge()](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.merge.html) function for this purpose.
- Store the results into a new dataframe called `trips_merged`
```python
# Merge trips with cars (joining on car_id)
trips_merged = trips.merge(TO COMPLETE)
```
Then continue by merging the new `trips_merged` dataframe with the `cities` dataframe
```python
# Merge with cities for car's city (joining on city_id)
trips_merged = trips_merged.merge(TO COMPLETE)
```
## Clean useless columns
- Use the dataframe drop() function to drop all the *id* columns. They are not useful for our analysis and dashboard.
```python
trips_merged = trips_merged.drop(columns=["id_car", "city_id", "id_customer", "id"])
```
## Commit and Push your  code at this point
- Commit and Push your  code to github at this point and provide a meaningful commment like *loading and cleaning car sharing data*

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

- Provide a component on the sidebar to let the user select or or multiple car models. You can use the streamlit function [multiselect()](https://docs.streamlit.io/develop/api-reference/widgets/st.multiselect) for this purpose. Use also the dataframe unique() function to retrieve the list of brands from the `trips_merged` dataframe
```python
cars_brand = st.sidebar.multiselect("Select the Car Brand", TO COMPLETE)
```
Once the user select the car brand, use the result to filter the dataframe on the `brand` column. Use the dataframe `isin()` function
```python
trips_merged = trips_merged[TO COMPLETE]
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
total_trips = TO COMPLETE  # Total number of trips
total_distance = TO COMPLETE  # Sum of all trip distances
# Car model with the highest revenue
top_car = TO COMPLETE

# Display metrics in columns
col1, col2, col3 = st.columns(3)
with col1:
    st.metric(label="Total Trips", value=total_trips)
with col2:
    st.metric(label="Top Car Model by Revenue", value=top_car)
with col3:
    st.metric(label="Total Distance (km)", value=f"{total_distance:,.2f}")
```
## Preview the contents of your dataframe
Use Streamlit write() function and the dataframe head() to display the first lines of your  dataframe `trips_merged` 
## Commit and Push your  code at this point 
Commit and Push your  code to github and give a meaningful comment like *adding business metrics*

# Visualizing the data
In this section, you shall use your data visualization knowledge to show nice dashboards with the data stored in your dataframe. 
## Visualization to be provided 
Provide at least three visualizations using streamlit functions line_chart(), bar_chart() or area_chart() among the following questions 
- Trips Over Time
- Revenue Per Car Model
- Cumulative Revenue Growth Over Time
- Number of Trips Per Car Model
- Average Trip Duration by city 
- Revenue by city
- your own visualization that you find relevant with this dataset ( bonus point)

## Commit and Push your  code 
At this point, commit and push your code to github and make sure the content on github is what you have on your codespace

## Deploy on Streamlit
- Login to https://share.streamlit.io with your github credentials
- If not yet done, create a new app and select your git repository
- Proceed to deploy, Copy the URL
## Share your assignment
- Copy the url to your streamlit app. It shall end with https://SOMENAME_YOU_HAVE_CHOSEN.streamlit.app
- Create a word document with **yourfirstname-yourlastname-streamlit-labs.doc
- Paste the url of your streamlit to the word document 
- Go to github and copy the url of your repository, shall be like https://github.com/dnuentsa/my-first-analytics-project 
- Paste the url of your github code to the word document
- Upload the word document to Campus under assignment section







