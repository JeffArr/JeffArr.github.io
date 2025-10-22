---
layout: post
title: Air Travel Connectivity Analysis
image: "/posts/flights_aiports-img.png"
tags: [Air Travel, Data Analysis, Python, Visualization]
---

In this post, I explore global air travel connectivity using the OpenFlights dataset. The project investigates which airports and airlines dominate global routes, the distribution of flight distances, and patterns across continents.

<br>

# Table of contents
- [Part I. Data Understanding](#data-understanding)
  - [About the Dataset](#about-the-dataset)
  - [Business Task](#business-task)
  - [Questions for Analysis](#questions-for-analysis)
- [Part II. Data Preparation](#data-preparation)
- [Part III. Analysis & Visualization](#analysis-visualization)
- [Part IV. Results & Insights](#results-insights)

# Part I. Data Understanding <a name="data-understanding"></a>

### About the Dataset <a name="about-the-dataset"></a>
This project utilizes three CSV files from the OpenFlights database: **airports**, **airlines**, and **routes**.  
These files provide detailed information about:
- **Airports** — name, location, IATA/ICAO codes, and coordinates.  
- **Airlines** — name, codes, and country of operation.  
- **Routes** — source and destination airports, airline, distance, and flight frequency.

Together, these datasets offer a global view of commercial air travel connectivity.

### Business Task <a name="business-task"></a>
The goal of this analysis is to identify the most connected **airports** and **airlines** worldwide, patterns in **long-distance** routes, and **regional trends** in connectivity that could guide investment and policy decisions.

### Questions for Analysis <a name="questions-for-analysis"></a>
- Which airports and airlines dominate global connectivity?  
- What are the shortest and longest average routes?  
- How does connectivity vary by continent or country?  
- Are there mismatches between dataset values and real-world flight volumes, and what might they imply about data reliability?

# Part II. Data Preparation <a name="data-preparation"></a>

### Context

Before beginning any analysis, the datasets need to be properly imported, cleaned, and prepared for exploration.  
In this stage, I combined three OpenFlights CSV files — **airports**, **airlines**, and **routes** — ensuring data consistency and readiness for analysis.

<br>

### Actions

Import required Python libraries:

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import pycountry_convert as pc
```
These libraries were used for data manipulation, visualization, and mapping countries to their respective continents.

<br>
Load datesets

```python
airlines = pd.read_csv("airlines.csv")
airports = pd.read_csv("airports.csv")
routes = pd.read_csv("routes.csv")
```
Each file contains a different aspect of the global air network:
* `airlines` – airline names, codes, and countries
* `airports` - airport locations, coordinates, and identifiers
* `routes ` - flight connections between airports, including operating airlines

Before cleaning, I reviewed the structure of each dataset to understand column names, data types, and null values:

```python
airlines.info()
airports.info()
routes.info()
```

### Data Cleaning
The raw data contained missing values and placeholder strings (\N) that needed to be standardized.

```python
airlines.replace("\\N", np.nan, inplace=True)
airports.replace("\\N", np.nan, inplace=True)
routes.replace("\\N", np.nan, inplace=True)

# Drop rows missing key identifiers
airports.dropna(subset=["IATA"], inplace=True)
airlines.dropna(subset=["IATA"], inplace=True)
routes.dropna(subset=["Source airport", "Destination airport"], inplace=True)
```

Check for duplicates

```python
print("Duplicate airports:", airports.duplicated(subset="IATA").sum())
print("Duplicate airlines:", airlines.duplicated(subset="IATA").sum())
```

Verifying Data Types and Cleaning Routes

```python
routes.replace('\\N', np.nan, inplace=True)
routes.drop(['Codeshare', 'Stops', 'Equipment'], axis=1, inplace=True)
routes.dropna(subset=['Source airport ID', 'Destination airport ID', 'Airline ID'], inplace=True)
```
Convert ID columns to integers:
``` python
routes['Airline ID'] = pd.to_numeric(routes['Airline ID'], errors='coerce').astype('Int64')
routes['Source airport ID'] = pd.to_numeric(routes['Source airport ID'], errors='coerce').astype('Int64')
routes['Destination airport ID'] = pd.to_numeric(routes['Destination airport ID'], errors='coerce').astype('Int64')
```
<br>

# Part III. Analysis & Visualization <a name="analysis-visualization"></a>

1. Airports with the most flights:
``` python
airports_flights = (
    pd.concat([
        routes['Source airport'],
        routes['Destination airport']
    ])
    .value_counts()
    .rename_axis('Airport')
    .reset_index(name = 'Total Flights')
    .sort_values(by = 'Total Flights', ascending = False)
    .merge(airports[['IATA','Name','Country','City','Latitude','Longitude']],
          left_on = 'Airport', right_on = 'IATA')
)
sns.set(style="whitegrid")
plt.figure(figsize=(10, 6))
sns.barplot(
    y='Name',
    x='Total Flights',
    data=airports_flights.head(10),
    color='steelblue'
)

plt.title('Top 10 Airports by Number of Flights')
plt.xlabel('Number of Flights')
plt.ylabel('Airport')
plt.show()

```
<img src="/img/posts/top_10_airpots_most_flights.png" alt="Top 10 Airports by Number of Flights" style="max-width: 100%; height: auto;">

Interpretation:
The visualization shows which airports serve as major global hubs. Expectedly, airports like ATL, LHR, and DXB appear as dominant centers of connectivity.

2. Most Connected Airlines

``` python
plt.figure(figsize=(10,6))
sns.barplot(
    data=unique_dest_airlines.head(10),
    y='Name',
    x='Unique Destinations',
    color='steelblue'
)

plt.title('Top 10 Airlines by Unique Destinations')
plt.xlabel('Unique Destinations')
plt.ylabel('Airline')
plt.tight_layout()
plt.show()
```


<img src="/img/posts/top_10_airlines_by_unique_destinations.png" alt="Top airlines by number of routes" style="max-width: 100%; height: auto;">

Major airlines such as American Airlines....


3. Connectivity by Continent
Countries were mapped to continents using the pycountry_convert library:

# Function to identify the continent for each country
```python
def country_to_continent(name):
    try:
        a2 = pc.country_name_to_country_alpha2(name)
        code = pc.country_alpha2_to_continent_code(a2)
        return pc.convert_continent_code_to_continent_name(code)
    except:
        return "Other"

airport_dest['Continent'] = airport_dest['Country'].apply(country_to_continent)

#roll up by continent
continent_dest = (airport_dest.groupby('Continent')['Unique Destinations']
                              .sum()
                              .sort_values(ascending=False)
                              .reset_index())

plt.figure(figsize=(8,5))
sns.barplot(data=continent_dest, x='Continent', y='Unique Destinations', color="steelblue")
plt.title('Unique Airport Destinations per Continent')
plt.xlabel('Continent')
plt.ylabel('Unique Destinations')
plt.tight_layout()
plt.show()
```
<img src="/img/posts/unique_airport_destinations_per_continent.png" alt="Air connectivity by continent" style="max-width: 100%; height: auto;">
Europe and North America dominate the global air network, followed by Asia.

4. Route Distance distribution

| **Route ID** | **Airline Code** | **Source Airport Code** | **Source Airport** | **Source Country** | **Destination Airport Code** | **Destination Airport** | **Destination Country** | **Distance (km)** | **Distance (mi)** |
|--------------:|------------------:|-------------------------:|-------------------:|-------------------:|------------------------------:|-------------------------:|-------------------------:|------------------:|------------------:|
| 6421 | AA | SYD | Sydney Kingsford Smith International Airport | Australia | DFW | Dallas Fort Worth International Airport | United States | 13808.20 | 8580.01 |
| 45870 | QF | SYD | Sydney Kingsford Smith International Airport | Australia | DFW | Dallas Fort Worth International Airport | United States | 13808.20 | 8580.01 |
| 20404 | DL | JNB | OR Tambo International Airport | South Africa | ATL | Hartsfield–Jackson Atlanta International Airport | United States | 13582.61 | 8439.84 |
| 19560 | DL | ATL | Hartsfield–Jackson Atlanta International Airport | United States | JNB | OR Tambo International Airport | South Africa | 13582.61 | 8439.84 |
| 13485 | B6 | DXB | Dubai International Airport | United Arab Emirates | LAX | Los Angeles International Airport | United States | 13400.10 | 8326.43 |


> **Table 1.** Major intercontinental routes ranked by great-circle distance.


# Part IV. Results & Insights <a name="results-insights"></a>

### Hub Dominance & Regional Patterns
- **ATL leads in flight volume** - Demonstrating U.S. domestic market strength
- **European hubs (LHR, CDG, FRA) excel in destination diversity** - Serving as global transfer points
- **Flight density varies significantly** - European airports show higher efficiency vs. North American fragmentation

### Airline Business Models
- **Ryanair dominates European short-haul** with low-cost, high-frequency model
- **U.S. legacy carriers balance volume & global coverage** - American and United lead in comprehensive networks
- **Partnership opportunities** between regional specialists and global carriers

### Global Connectivity Gaps
- **North America & Europe dominate** current air travel networks
- **Africa & South America remain underconnected** - Presenting long-term growth opportunities
- **Ultra-long-haul routes emerging** - Sydney–Dallas, Johannesburg–Atlanta signaling new market potentials


If you’d like to view the source notebook, you can find it here: 
<a href="https://cogs119.github.io/group_fa24_false_recall](https://github.com/JeffArr/air-travel-connectivity/blob/main/Air_Travel_Connectivity_Report.ipynb)/" target="_blank">Notebook</a>


