# Visualizing Sea Level Rise Across Puerto Rico (2015–2025)

## Project Description
This project analyzes sea level data from the NOAA Tides & Currents API for Puerto Rico, focusing on four coastal tide stations:
San Juan (North), Parguera (South), Vieques Island (East), and Mayagüez (West). Using data from January 2015 to January 2025, the 
primary aim is to identify long-term trends in sea level rise and visualize the regional variations across the island's coasts. By 
investigating the data, the project seeks to uncover patterns in how natural disasters and environmental change have impacted sea 
levels over the past decade. These findings will help in understanding how Puerto Rico’s coastal areas may be at risk from ongoing 
environmental shifts.

## Rationale Statement:
The rationale behind this project is to investigate how sea levels have changed in Puerto Rico's coastal regions over time and to
understand the broader implications of those changes. While sea level rise is a well-established global issue, the specific effects
on Puerto Rico remain underexplored, especially in the context of natural disasters. By using historical data from NOAA's Tides & 
Currents API, this project aims to provide valuable insights into the temporal and spatial patterns of sea level rise, which could
serve as a foundation for future research on environmental risks and community vulnerability. This analysis will provide data-driven
insights that can contribute to the ongoing conversation about Puerto Rico’s environmental resilience.

## Workflow:

### Overview
The project involved collecting and analyzing sea level data from four tide stations across Puerto Rico (PR) between 2015 and 2025. The process utilized Python libraries such as requests, pandas, and Plotly to retrieve, clean, organize, and visualize the data to identify trends in sea level rise across the region.


### 1. Imports:
I imported the necessary libraries in order to make the API call to fetch the data and to manipulate the data.
```python
import requests
import pandas as pd
```

### 2. API Request Setup and Call:
I set up the parameters for the API request such as the dates, station IDs, product type, etc and store the result.

```python
url = "https://api.tidesandcurrents.noaa.gov/api/prod/datagetter"
params = {
    "begin_date": "20150101",
    "end_date": "20250101",
    "station": "9752695",  # Vieques
    "product": "monthly_mean",
    "datum": "MSL",
    "units": "metric",
    "time_zone": "gmt",
    "format": "json"
}

python
response = requests.get(url, params=params)
```

### 3. Checking Response and Processing Data:
I checked if the API call was successful. I converted the data into a pandas DataFrame.

```python
if response.status_code == 200:
    data = response.json()
    if "data" in data:
        df = pd.DataFrame(data["data"])
```

### 4. Data Cleaning & Calculating Averages:
I organized the dates into separate columns, converted the 'MSL' to numeric values, and removed the rows where any data is missing. I grouped the data and the MSL results were rounded to 3 decimal places.

```python
df['date'] = pd.to_datetime(df[['year', 'month']].assign(day=1))
df["MSL"] = pd.to_numeric(df["MSL"], errors='coerce')
df.dropna(subset=["date", "MSL"], inplace=True)
df['year'] = df['date'].dt.year

python
yearly_avg = df.groupby('year')['MSL'].mean().round(3)
```

### 5. Exporting the Results:
I saved the yearly singular station averages to a CSV file. I repeated this whole process across the four stations.

```python
yearly_avg.to_csv('yearly_avg_sea_level.csv')
```

Here's a generalized and polished continuation from where you left off — suitable for documenting your workflow clearly while reflecting that the process was repeated across multiple stations and then aggregated:

### 6. Looping Through Multiple Stations:
To analyze trends across Puerto Rico more broadly, I repeated the data collection and cleaning steps for multiplethe four NOAA tide stations. Using the NOAA assigned station IDs and iterated through them, appending the cleaned data for each into a single DataFrame.

```python
stations = ["9759394", "9759110", "9755371", "9752695"]  # Mayagüez, Parguera, San Juan, Vieques
all_station_data = pd.DataFrame()

for station_id in stations:
    params["station"] = station_id
    response = requests.get(url, params=params)

    if response.status_code == 200:
        data = response.json()
        if "data" in data:
            df = pd.DataFrame(data["data"])
            df['date'] = pd.to_datetime(df[['year', 'month']].assign(day=1))
            df["MSL"] = pd.to_numeric(df["MSL"], errors='coerce')
            df.dropna(subset=["date", "MSL"], inplace=True)
            df["station"] = station_id
            all_station_data = pd.concat([all_station_data, df])
```

### 7. Aggregating Monthly and Yearly Averages:
After compiling the data from all stations, I calculated overall monthly and yearly averages across the entire island. 

```python
# Monthly
all_station_data['year_month'] = all_station_data['date'].dt.to_period('M')
monthly_avg = all_station_data.groupby('year_month')['MSL'].mean().reset_index()
monthly_avg["MSL"] = monthly_avg["MSL"].round(3)

# Yearly
all_station_data['year'] = all_station_data['date'].dt.year
yearly_avg = all_station_data.groupby('year')['MSL'].mean().reset_index()
yearly_avg["MSL"] = yearly_avg["MSL"].round(3)
```

### 8. Saving Aggregated Results:
I exported the final monthly and yearly island-wide sea level averages to CSV files for documentation or further analysis/visualization.

```python
monthly_avg.to_csv('monthly_avg_sea_level_data.csv', index=False)
yearly_avg.to_csv('yearly_avg_sea_level_data.csv', index=False)
```

### 9. Output Preview:
I printed both sets of averages for a quick summary view.

```python
print("Monthly Island-wide Averages:")
print(monthly_avg)

print("\nYearly Island-wide Averages:")
print(yearly_avg)
```
Great — here's a continuation from your previous workflow into the graphing phase, generalized to reflect that this is one of multiple visualizations but follows a consistent process:

### 10. Visualizing the Results:
To better understand and communicate the trends in sea level rise across Puerto Rico, I created interactive charts using Plotly. Although I created three different types of visualizations, the general process involved loading the final CSV files and plotting the relevant columns. Below is an example of one of the charts I created — a bar graph of yearly sea level averages.

```python
import plotly.express as px
import pandas as pd

df = pd.read_csv('PR_Yearly_Averages_FINAL.csv')

fig = px.bar(
    df, 
    x='year', 
    y='MSL', 
    title='Yearly Sea Level Averages Across Puerto Rico (2015–2025)',
    labels={'MSL': 'Mean Sea Level (meters)', 'year': 'Year'}
)

fig.show()
fig.write_html("sea_level_chart.html")
```

## Further Uses:
This project provides an opportunity to create a coastal risk map for Puerto Rico, highlighting areas most vulnerable to sea level rise. By visualizing trends in sea level data, we can better understand how coastal communities, infrastructure, and ecosystems are being impacted. This analysis also offers a lens through which to track the influence of U.S. intervention, La Junta, LUMA, and real estate investors in shaping Puerto Rico's coastal resilience. By examining their roles and actions, we can assess whether current policies and investments align with the island's long-term environmental and conservation needs, contributing to broader discussions about sustainable development and disaster preparedness.

## Files List:
### 1. Production Files
- Bargraph-PR_Yearly_Averages.ipynb
- Lineplot_PR.ipynb
- heatmap_PR-monthly.ipynb
- VIEQUES_Yearly-Averages_FINAL.csv
- SJ_Yearly-Averages_FINAL.csv
- MAYAGUEZ_Yearly-Averages_FINAL.csv
- PARGUERA_Yearly-Averages_FINAL.csv
- PR_Monthly-Averages_FINAL.csv
- PR_Yearly_Averages_FINAL.csv

### 2. Output Files
- Heatmap_PR_monthly.html
- Linegraph_4stations.html
- sea_level_chart.html
- Background.jpeg
- PR_Map_final.png
- NOAA_TC-logo.png

### 3. Web Files
- index.html
- style.css

### 4. Doocumentation
- README.md
