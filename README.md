# Archie-Hulse-GeoData-Project
Project to show the optimal location for a new business to be located in order to develop.

**GeoSpatial Data:**
- To show the optimal location for a new business to grow based on the set parameters of:
    - Designers like to go to design talks and share knowledge. There must be some nearby companies that also do design.
    - 30% of the company staff have at least 1 child.
    - Developers like to be near successful tech startups that have raised at least 1 Million dollars.
    - Executives like Starbucks A LOT. Ensure there's a starbucks not too far.
    - Account managers need to travel a lot.
    - Everyone in the company is between 25 and 40, give them some place to go party.
    - The CEO is vegan.

- Firstly, data of global comapnies was obtained and inserted into Mongo. This was then broken down in python using a complex code to achieve a pandas dataframe:



#Data:
import json
import requests
import pandas as pd
import numpy as np
from pymongo import MongoClient
import pandas as pd

#Map:
import folium
from folium import Choropleth, Circle, Marker, Icon, Map
from folium.features import CustomIcon
from folium.plugins import HeatMap, MarkerCluster, FastMarkerCluster
import geopandas as gpd
from cartoframes.viz import Map, Layer, popup_element, style
from shapely.geometry import Point
import branca.colormap as cm

#visualisation:
#import seaborn as sns
import matplotlib.pyplot as plt
#import matplotlib as mpl
#from cycler import cycler
#import hvplot.pandas
#import lxml.html




client = MongoClient("localhost:27017")
db = client["ironhack"]
c = db.get_collection("companies")
print(c)
db.list_collection_names()
companies = db["companies"]

pipeline = [
    {
        "$match": {
            "category_code": {
                "$in": [
                    "analytics",
                    "biotech",
                    "cleantech",
                    "ecommerce",
                    "messaging",
                    "mobile",
                    "nanotech",
                    "social",
                    "software",
                    "web",
                    "games_video"
                ]
            },
            "founded_year": {"$gte": 2010}
        }
    },
    {
        "$unwind": "$funding_rounds"
    },
    {
        "$match": {
            "funding_rounds.raised_amount": { "$gte": 1000000 }
        }
    },
    {
        "$group": {
            "_id": "$_id",
            "name": { "$first": "$name" },
            "offices": { "$first": "$offices" },
            "category_code": { "$first": "$category_code" },
            "founded_year": { "$first": "$founded_year" },
            "raised_amount": { "$sum": "$funding_rounds.raised_amount" }
        }
    },
    {
        "$match": {
            "raised_amount": { "$gte": 1000000 }
        }
    }
]

- This was then plotted onto a empty map of the globe in heat format in order to determin the distribution of tech startups gloablly from the data, that had alo raised 1 million USD already.

- From the data it could be infered that San Francisco and more specfically San Mateo was the location with the highest frequency of said startups.

- This process was repeated, tapping into the same dataset but this time filtering for Design businesses globally. Since My business has employees that want to be located near to Design and Tech Startup buesinesses. As shown in the two maps below San Mateo has good coverage for both Tech Startups and Design Companies:




result = list(companies.aggregate(pipeline))
print(len(result))

data_tech = pd.DataFrame(result)
data_tech.sample()


data = []
for company in result:
    for office in company['offices']:
        data.append({
            'name': company['name'],
            'office description': office['description'],
            'office latitude': office['latitude'],
            'office longitude': office['longitude']
        })

# Convert the list into a Pandas DataFrame
df = pd.DataFrame(data)

# Rearrange the columns in the desired order
df = df[['name', 'office description', 'office latitude', 'office longitude']]

df2 = df.drop(columns=['office description'])
df3 = df2.dropna(subset=['office latitude','office longitude'])
df4 = df3.dropna().reset_index(drop=True)
df4.head()


tech_heat_map = folium.Map(location=[-3.702818, 40.443921], zoom_start=2)

heat_data = [[row['office latitude'], row['office longitude']] for index, row in df4.iterrows()]

folium.plugins.HeatMap(heat_data, radius=10).add_to(tech_heat_map)

tech_heat_map



pipeline2 = [
    {
        "$match": {
            "category_code": {
                "$in": [
                    "design",
                    "fashion",
                    "advertising"]}}}, 
        {"$group": {
            "_id": "$_id",
            "name": { "$first": "$name" },
            "offices": { "$first": "$offices" },
            "category_code": { "$first": "$category_code" },
        }
    }]


result2 = list(companies.aggregate(pipeline2))
print(len(result2))

data_design = pd.DataFrame(result2)
data_design.head()


data = []
for company in result2:
    for office in company['offices']:
        data.append({
            'name': company['name'],
            'office description': office['description'],
            'office latitude': office['latitude'],
            'office longitude': office['longitude']
        })

# Convert the list into a Pandas DataFrame
df_design = pd.DataFrame(data)

# Rearrange the columns in the desired order
df_design = df_design[['name', 'office description', 'office latitude', 'office longitude']]

df_design2 = df_design.drop(columns=['office description'])
df_design3 = df_design2.dropna(subset=['office latitude','office longitude'])
df_design4 = df_design3.dropna().reset_index(drop=True)
df_design4.head()

design_heat_map = folium.Map(location=[-3.702818, 40.443921], zoom_start=2)

heat_data2 = [[row['office latitude'], row['office longitude']] for index, row in df_design4.iterrows()]

folium.plugins.HeatMap(heat_data2, radius=10).add_to(design_heat_map)

design_heat_map


- These data set were then saved in .csv files and used for the final map presentation.

- An API from foursquare.com provided the ability to search for nearest schools, taxi ranks, bars, Starbucks, aiport and others in San Mateo.
- These API were imported to python and then converted to pandas dataframes, where they were then plotted onto my global map as markers.

- The .csv files of the positional locations for all of the nearby Tech Startup and Design Companies were coverted back into dataframes and plotted as markers onto the map with corresponding colours and icons.

- finally, the mean lattitude and longitude of all of these parameter locations was calculated from the data in order to pin point the oprimal location of My company in San Mateo:




client = MongoClient("localhost:27017")
db = client["ironhack"]
c = db.get_collection("companies")
print(c)
db.list_collection_names()
companies = db["companies"]


#API SCHOOLS:
url = "https://api.foursquare.com/v3/places/search?query=school&ll=37.562992%2C-122.325523&radius=100000&sort=DISTANCE&limit=10"

headers = {
    "accept": "application/json",
    "Authorization": "fsq3r/TpfzZa1Jt2dzQr5+9KGqJwk9feYwt5/xcu0g6pewc="
}

response = requests.get(url, headers=headers)
results = response.json()["results"]
print(len(results))

df_shcool = pd.DataFrame(results)


#DATAFRAME:
df_shcool2 = df_shcool.drop(columns=["chains", "fsq_id", "link", "related_places", "timezone", "categories", "location"])

def extract_lat_long(row):
    latitude = row['geocodes']['main']['latitude']
    longitude = row['geocodes']['main']['longitude']
    return pd.Series({'school_latitude': latitude, 'school_longitude': longitude})

df_shcool2[['school_lat', 'school_lon']] = df_shcool2.apply(extract_lat_long, axis=1)

df_shcool3 = df_shcool2.drop(columns=["geocodes"])
df_shcool4 = df_shcool3.drop(index=[0,1,2,3,4,6,8])
df_shcool4.reset_index(drop=True, inplace=True)
df_shcool4




#API AIRPORT:
url2 = "https://api.foursquare.com/v3/places/search?query=internationalairport&ll=37.562992%2C-122.325523&radius=100000&sort=DISTANCE&limit=3"

headers = {
    "accept": "application/json",
    "Authorization": "fsq3r/TpfzZa1Jt2dzQr5+9KGqJwk9feYwt5/xcu0g6pewc="
}

response2 = requests.get(url2, headers=headers)
results2 = response2.json()["results"]
print(len(results2))

df_airport = pd.DataFrame(results2)

#DATAFRAME:
df_airport2 = df_airport.drop(columns=["chains", "fsq_id", "link", "related_places", "timezone", "categories", "location"])

def extract_lat_long(row):
    latitude = row['geocodes']['main']['latitude']
    longitude = row['geocodes']['main']['longitude']
    return pd.Series({'airport_latitude': latitude, 'airport_longitude': longitude})

df_airport2[['airport_lat', 'airport_lon']] = df_airport2.apply(extract_lat_long, axis=1)

df_airport3 = df_airport2.drop(columns=["geocodes"])
df_airport4 = df_airport3.drop(index=[0,1])
df_airport4.reset_index(drop=True, inplace=True)
df_airport4




#API STARBUCKS:
url3 = "https://api.foursquare.com/v3/places/search?query=starbucks&ll=37.562992%2C-122.325523&radius=100000&sort=DISTANCE&limit=4"

headers = {
    "accept": "application/json",
    "Authorization": "fsq3r/TpfzZa1Jt2dzQr5+9KGqJwk9feYwt5/xcu0g6pewc="
}

response3 = requests.get(url3, headers=headers)
results3 = response3.json()["results"]
print(len(results3))

df_starbucks = pd.DataFrame(results3)

#DATAFRAME:
df_starbucks2 = df_starbucks.drop(columns=["chains", "fsq_id", "link", "related_places", "timezone", "categories", "location"])

def extract_lat_long(row):
    latitude = row['geocodes']['main']['latitude']
    longitude = row['geocodes']['main']['longitude']
    return pd.Series({'starbucks_latitude': latitude, 'starbucks_longitude': longitude})

df_starbucks2[['starbucks_lat', 'starbucks_lon']] = df_starbucks2.apply(extract_lat_long, axis=1)

df_starbucks3 = df_starbucks2.drop(columns=["geocodes"])
df_starbucks4 = df_starbucks3.drop(index=[1,2,3])
df_starbucks4.reset_index(drop=True, inplace=True)
df_starbucks4



#API TAXI:
url4 = "https://api.foursquare.com/v3/places/search?query=taxi&ll=37.562992%2C-122.325523&radius=100000&sort=DISTANCE&limit=20"

headers = {
    "accept": "application/json",
    "Authorization": "fsq3r/TpfzZa1Jt2dzQr5+9KGqJwk9feYwt5/xcu0g6pewc="
}

response4 = requests.get(url4, headers=headers)
results4 = response4.json()["results"]
print(len(results4))

df_taxi = pd.DataFrame(results4)

#DATAFRAME:
df_taxi2 = df_taxi.drop(columns=["chains", "fsq_id", "link", "related_places", "timezone", "categories", "location"])

def extract_lat_long(row):
    latitude = row['geocodes']['main']['latitude']
    longitude = row['geocodes']['main']['longitude']
    return pd.Series({'taxi_latitude': latitude, 'taxi_longitude': longitude})

df_taxi2[['taxi_lat', 'taxi_lon']] = df_taxi2.apply(extract_lat_long, axis=1)

df_taxi3 = df_taxi2.drop(columns=["geocodes"])
df_taxi4 = df_taxi3.drop(index=[0,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19])
df_taxi4.reset_index(drop=True, inplace=True)
df_taxi4



#API VEGAN RESTAURANT:
url5 = "https://api.foursquare.com/v3/places/search?query=vegan&ll=37.562992%2C-122.325523&radius=100000&sort=DISTANCE&limit=5"

headers = {
    "accept": "application/json",
    "Authorization": "fsq3r/TpfzZa1Jt2dzQr5+9KGqJwk9feYwt5/xcu0g6pewc="
}

response5 = requests.get(url5, headers=headers)
results5 = response5.json()["results"]
print(len(results5))

df_Vegan = pd.DataFrame(results5)

#DATAFRAME:
df_Vegan2 = df_Vegan.drop(columns=["chains", "fsq_id", "link", "related_places", "timezone", "categories", "location"])

def extract_lat_long(row):
    latitude = row['geocodes']['main']['latitude']
    longitude = row['geocodes']['main']['longitude']
    return pd.Series({'vegan_latitude': latitude, 'vegan_longitude': longitude})

df_Vegan2[['vegan_lat', 'vegan_lon']] = df_Vegan2.apply(extract_lat_long, axis=1)

df_Vegan3 = df_Vegan2.drop(columns=["geocodes"])
df_Vegan4 = df_Vegan3.drop(index=[1,2,3,4])
df_Vegan4.reset_index(drop=True, inplace=True)
df_Vegan4




#API SURF SHOP:
url6 = "https://api.foursquare.com/v3/places/search?query=surf&ll=37.562992%2C-122.325523&radius=100000&sort=DISTANCE&limit=10"

headers = {
    "accept": "application/json",
    "Authorization": "fsq3r/TpfzZa1Jt2dzQr5+9KGqJwk9feYwt5/xcu0g6pewc="
}

response6 = requests.get(url6, headers=headers)
results6 = response6.json()["results"]
print(len(results6))

df_surfshop = pd.DataFrame(results6)

#DATAFRAME:
df_surfshop2 = df_surfshop.drop(columns=["chains", "fsq_id", "link", "related_places", "timezone", "categories", "location"])

def extract_lat_long(row):
    latitude = row['geocodes']['main']['latitude']
    longitude = row['geocodes']['main']['longitude']
    return pd.Series({'surf_latitude': latitude, 'surf_longitude': longitude})

df_surfshop2[['surf_lat', 'surf_lon']] = df_surfshop2.apply(extract_lat_long, axis=1)

df_surfshop3 = df_surfshop2.drop(columns=["geocodes"])
df_surfshop4 = df_surfshop3.drop(index=[0,1,2,3,5,6,7,8,9])
df_surfshop4.reset_index(drop=True, inplace=True)
df_surfshop4



#API BAR:
url7 = "https://api.foursquare.com/v3/places/search?query=bar&ll=37.562992%2C-122.325523&radius=100000&sort=DISTANCE&limit=5"

headers = {
    "accept": "application/json",
    "Authorization": "fsq3r/TpfzZa1Jt2dzQr5+9KGqJwk9feYwt5/xcu0g6pewc="
}

response7 = requests.get(url7, headers=headers)
results7 = response7.json()["results"]
print(len(results7))

df_bar = pd.DataFrame(results7)

#DATAFRAME:
df_bar2 = df_bar.drop(columns=["chains", "fsq_id", "link", "related_places", "timezone", "categories", "location"])

def extract_lat_long(row):
    latitude = row['geocodes']['main']['latitude']
    longitude = row['geocodes']['main']['longitude']
    return pd.Series({'bar_latitude': latitude, 'bar_longitude': longitude})

df_bar2[['bar_lat', 'bar_lon']] = df_bar2.apply(extract_lat_long, axis=1)

df_bar3 = df_bar2.drop(columns=["geocodes"])
df_bar4 = df_bar3.drop(index=[0,1,2,4])
df_bar4.reset_index(drop=True, inplace=True)
df_bar4



#TECH STARTUPS:
tech_data = pd.read_csv('data/tech_companies.csv')
df_tech = tech_data
df_tech2 = df_tech.drop(index=[0,1,2,3,4,5,7,8,10,11,13,14,15,16,19,20,21,22,23,24,25,27,28,29,30,31,32,17])
df_tech3 = df_tech2.drop(columns=["office description"])
df_tech3.reset_index(drop=True, inplace=True)
df_tech3


#DESIGN COMAPANIES:
design_data = pd.read_csv('data/design_companies.csv')
df_design = design_data
df_design2 = df_design[(df_design['office latitude'] >= 37.4) & (df_design['office latitude'] <= 37.6) &
        (df_design['office longitude'] >= -122.6) & (df_design['office longitude'] <= -122.2)]

df_design3 = df_design2.drop(index=[87,269,342,425,634,308,490,19,527,])
df_design3.reset_index(drop=True, inplace=True)
df_design3.head()


#MY COMPANY LOCATION:

#triangulate the middle point by mean of marker lats and lons:
mean_latitude_s = np.mean(df_shcool4["school_lat"])
mean_longitude_s = np.mean(df_shcool4["school_lon"])
mean_latitude_t = np.mean(df_tech3["office latitude"])
mean_longitude_t = np.mean(df_tech3["office longitude"])
mean_latitude_d = np.mean(df_design3["office latitude"])
mean_longitude_d = np.mean(df_design3["office longitude"])

mean_latitudes = [mean_latitude_s, mean_latitude_t, mean_latitude_d]
mean_longitudes = [mean_longitude_s, mean_longitude_t, mean_longitude_d]
mean_latitude = np.mean(mean_latitudes)
mean_longitude = np.mean(mean_longitudes)

print(mean_latitude)
print(mean_longitude)

#manually crate a dataframe so that it can be input to the map
data_company = {'name':['My Comapny: 37.55N -122.29W'],
        'office latitude':[37.55944039142857],
        'office longitude':[-122.29696910984127]}


df_my_comapny = pd.DataFrame(data_company)
df_my_comapny


# Create a folium map object centered around the mean latitude and longitude of the schools
mean_latitude = np.mean(df_shcool4["school_lat"])
mean_longitude = np.mean(df_shcool4["school_lon"])
map_osm = folium.Map(location=[mean_latitude, mean_longitude], zoom_start=12)

#base = 'cartodbpositron'

#Create a transparent layer
transparent_layer = folium.TileLayer(
    tiles='Stamen Water Color',
    attr='<a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, &copy; <a href="http://cartodb.com/attributions">CartoDB</a>',
    name='Transparent',
    opacity=0.5
)
#Add the layer to your map
transparent_layer.add_to(map_osm)

#add my office to the map
for i, comapny in df_my_comapny.iterrows():
    marker = Marker(
        location=[df_my_comapny["office latitude"], df_my_comapny["office longitude"]],
        icon=Icon(icon='fa-building', prefix='fa', color='red')
    )
    marker.add_child(folium.Popup(comapny["name"]))
    marker.add_to(map_osm)

# Create a marker for each school in the data frame and add it to the map
for i, school in df_shcool4.iterrows():
    marker = Marker(
        location=[school["school_lat"], school["school_lon"]],
        icon=Icon(icon='fa-graduation-cap', prefix='fa', color='blue')
    )
    marker.add_child(folium.Popup(school["name"]))
    marker.add_to(map_osm)

# Do the same for the airports and Starbucks etc, using different colors and icons
for i, airport in df_airport4.iterrows():
    marker = Marker(
        location=[airport["airport_lat"], airport["airport_lon"]],
        icon=Icon(icon='fa-plane', prefix='fa', color='lightred')
    )
    marker.add_child(folium.Popup(airport["name"]))
    marker.add_to(map_osm)

for i, starbucks in df_starbucks4.iterrows():
    marker = Marker(
        location=[starbucks["starbucks_lat"], starbucks["starbucks_lon"]],
        icon=Icon(icon='fa-coffee', prefix='fa', color='green')
    )
    marker.add_child(folium.Popup(starbucks["name"]))
    marker.add_to(map_osm)

for i, taxi in df_taxi4.iterrows():
    marker = Marker(
        location=[taxi["taxi_lat"], taxi["taxi_lon"]],
        icon=Icon(icon='fa-taxi', prefix='fa', color='black')
    )
    marker.add_child(folium.Popup(taxi["name"]))
    marker.add_to(map_osm)

for i, vegan in df_Vegan4.iterrows():
    marker = Marker(
        location=[vegan["vegan_lat"], vegan["vegan_lon"]],
        icon=Icon(icon='fa-cutlery', prefix='fa', color='lightgreen')
    )
    marker.add_child(folium.Popup(vegan["name"]))
    marker.add_to(map_osm)

for i, surf in df_surfshop4.iterrows():
    marker = Marker(
        location=[surf["surf_lat"], surf["surf_lon"]],
        icon=Icon(icon='fa-shopping-bag', prefix='fa', color='orange')
    )
    marker.add_child(folium.Popup(surf["name"]))
    marker.add_to(map_osm)

for i, bar in df_bar4.iterrows():
    marker = Marker(
        location=[bar["bar_lat"], bar["bar_lon"]],
        icon=Icon(icon='fa-beer', prefix='fa', color='beige')
    )
    marker.add_child(folium.Popup(bar["name"]))
    marker.add_to(map_osm)

for i, tech in df_tech3.iterrows():
    marker = Marker(
        location=[tech["office latitude"], tech["office longitude"]],
        icon=Icon(icon='fa-cogs', prefix='fa', color='purple')
    )
    marker.add_child(folium.Popup(tech["name"]))
    marker.add_to(map_osm)

for i, design in df_design3.iterrows():
    marker = Marker(
        location=[design["office latitude"], design["office longitude"]],
        icon=Icon(icon='fa-paint-brush', prefix='fa', color='cadetblue')
    )
    marker.add_child(folium.Popup(design["name"]))
    marker.add_to(map_osm)

# Save the map as an HTML file
map_osm.save("map.html")
