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


## Further Uses:
The project would provide a foundation for future projects that aim to explore the socioeconomic impacts of sea level rise, including real estate development, land use changes, and the vulnerability of coastal communities. Future uses could also involve broadening the amount of time analyzed or geographically expanding to other regions. 

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

  
