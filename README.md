# USING GEOPANDAS AND FOLIUM IN TANDEM: A GEOSPATIAL EXPLORATION IN RETROGRADE
A tutorial/interactive experience for using both GeoPandas and Folium libraries 

Created by: Rachel Corcoran-Adams for IDCE 30274, November 2020

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
