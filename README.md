# Using GeoPandas and Folium in Tandem: A Geospatial Exploration using Retrograde Problem Solving
Created by: Rachel Corcoran-Adams for IDCE 30274, November 2020

A tutorial/interactive experience for using both GeoPandas and Folium libraries. According to Astell-Burt et al. (2014), lack of green space accessibility and the inequitable distribution of parks is likely to exacerbate health inequalities and crime levels. Worcester, Massachusetts is considered an environmental justice zone with a high population of citizens with an average household income well below the state average, an average education level below the state average and high renter population. In this tutorial, we will be re-creating a geospatial vector analysis comparing the number of households in Worcester with no access to a vehicle and the locations of open space.



In this tutorial, we will be exploring the ways in which 'geopandas', and 'folium' can be used in Python to pre-process, geoprocess and display vector shapefiles. 

### Data
Data for this tutorial can be obtained from the 'data' folder of this repository. In this folder, there are shapefiles downloaded from:
Census tract town boundaries of Worcester, MA - [here](https://docs.digital.mass.gov/dataset/massgis-data-community-boundaries-towns-survey-points)
Open space data from MassGIS - [here](https://docs.digital.mass.gov/dataset/massgis-data-protected-and-recreational-openspace)
Land cover (2016) from MassGIS - [here](https://docs.digital.mass.gov/dataset/massgis-data-2016-land-coverland-use)
American Community Survey 2014-2016 from the Living Atlas - [here](https://www.arcgis.com/home/item.html?id=9a9e43ec1603446880c50d4ed1df2207)

# Part 1: Importing
```python
!pip install geopandas
!pip install gdal
!pip install rtree
!apt-get install -y libspatialindex-dev
!install -g topojson
import gdal
import matplotlib.pyplot as plt
import rtree
import folium
import os
import json
from folium import plugins
import geopandas as gpd
from shapely.geometry import Point, Polygon, MultiPolygon
from shapely import wkt
```
Importing data to Google Drive helps when working with Google Colab.
```python 
# import files from Drive
from google.colab import drive
drive.mount('/content/gdrive')
# set root path
root_path = 'gdrive/My Drive/GIS/final_project/'
```

# Part 2: Data Pre-Processing
```python
# import all of the shapefiles 
open_space = gpd.read_file(root_path+'open_space_clipped_1.shp')
ACS_Worcester = gpd.read_file(root_path+'ACS_Clipped_final_1.shp')
census_tracts = gpd.read_file(root_path+'CENSUS2010TRACTS_POLY.shp')
census_towns = gpd.read_file(root_path+ 'CENSUS2010TOWNS_POLY.shp')
landcover = gpd.read_file(root_path+ 'landcover_clipped.shp')
vehicle_access = gpd.read_file(root_path+ 'vehicle_access.shp')
```
I then projected all of my shapefiles to EPSG:4326, which is the required coordinate system for geojson. 
```python
# project all the shapefiles 
open_space = open_space.to_crs("EPSG:4326")
ACS_Worcester = open_space.to_crs("EPSG:4326")
census_tracts = census_tracts.to_crs("EPSG:4326")
census_towns = census_towns.to_crs("EPSG:4326")
landcover = landcover.to_crs("EPSG:4326")
vehicle_access = vehicle_access.to_crs("EPSG:4326")
```
Check to see if it worked!
```python
#check to see if it worked!
open_space.crs
```
```python
#check to see if it worked!
ACS_Worcester.crs
```
```python
#check to see if it worked!
census_towns.crs
```
```python
#check to see if it worked!
landcover.crs
```
```python
#check to see if it worked!
vehicle.crs
```
This is the output:

![crs](images/crs_output2.png)

# Part 3: Geoprocessing with GeoPandas
```python
# This line selects by attribute to create a worcester polygon
worcester = census_towns[census_towns['TOWN']=="WORCESTER"]
# This line clips the open space file to just include worcester
open_space_clipped = gpd.clip(open_space, worcester)
# This line selects by attribute for open space to include public access
open_space_select = open_space_clipped[open_space_clipped['PUB_ACCESS']=="Y"]
# This line selects only polygons with full public access and with primary purpose of recreation and conservation
pub_access_recreation = open_space_select[open_space_select['PRIM_PURP']== "B"] 
# This line will be selecting only the residential multi-family features in landcover 
landcover_residential = landcover[landcover['USEGENCODE']== "Residential-multi-family"]
```

# Part 4: Converting to GeoJSON
```python
# Creates a function to convert the shapefiles into geojson 
def shapefile2geojson(infile, outfile, fieldname):
    options = gdal.VectorTranslateOptions(format="GeoJSON",
                                          dstSRS="EPSG:4326")
    gdal.VectorTranslate(outfile, infile, options=options)
shapefile2geojson('pub_access_recreation.geojson', 'pub_access_recreation.shp', 'SITE_NAME')
shapefile2geojson('landcover_residential.geojson', 'landcover_residential.shp', 'COVERNAME')
shapefile2geojson('worcester.geojson', 'worcester.shp', 'TOWN')
shapefile2geojson('vehicle_access.geojson', 'vehicle_access.shp', 'B08201_002E')
```

# Part 5: Making the final map!
```python
# Shows the vehicle access Geojson
bins = list(vehicle_access['TARGET_FID'].quantile([0,0.25,0.5,0.75,1]))
folium.GeoJson(
    vehicle_access
).add_to(Vehicle_map)
Vehicle_map
```
This is the output:

![folium](images/folium_output2.png)
