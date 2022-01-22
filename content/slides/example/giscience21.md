---
marp: true
title: VASA
size: 4:3
keywords: gis, moran
description: this is a description
footer: Poznan, Poland (2021) - Noi, Rudolph & Dodge
---
<!-- <style>
section {
  background: yellow;
}
</style> -->
<style>
h1 {
  color: #31708f ;
}

div.container {
      display:inline-block;
      padding: 5px;
    }

.img1 {
  border-radius: 50%;
}

section.lead h1 {
  text-align: center;
}

mark {
  background-color: #5ec3ae;
  color: white;
}
</style>

# VASA: mapping spatiotemporal structure of mobility patterns during the COVID-19 pandemic

### Evgeny Noi, Alexander Rudolph, Somayeh Dodge

> GIScience 2021, Poznan, Poland (Virtual)

<!-- ||||
|---|---|---|
|![](https://www.giscience.org/img/patrons.png)|![width:150](https://move.geog.ucsb.edu/wp-content/uploads/2021/08/cropped-MOVE_final_nobackground.png)|![width:150](https://i.imgur.com/YxCk4Qb.png)| -->

<div class="image123">
<center>
  <div class="container">
    <img src="https://www.giscience.org/img/patrons.png" width="200" />
  </div>
  <div class="container">
    <img class="middle-img" src="https://move.geog.ucsb.edu/wp-content/uploads/2021/08/cropped-MOVE_final_nobackground.png" width="200" />
  </div>
  <div class="container">
    <img src="https://i.imgur.com/YxCk4Qb.png" width="200" />
  </div>
  <div class="container">
  <img src="https://www.giscience.org/img/giscience_icon.png" width="200" />
  </div>
  </center>
</div>


---

<!-- _backgroundColor: #31708f -->
<!-- _color: white-->
<!-- paginate: true -->
<!-- _footer: '' -->

## <!-- fit --> I. The problem :dart:


---

# Introduction

- First and second order spatial effects violate assumptions of classic inferential statistic (independence and iid)
- Exploratory tools from spatial statistics and econometrics that help us assess phenomena while taking into consideration these spatial effects
  - Moran's I, Geary's C, Ripley's K, etc.
  - ~~Space-time data-mining and visual analytics~~  

---

# Problem 

- Most common visual display in the geostack (PySAL, GeoDa, ArcGIS, QGIS, spdep, etc.) utilize choropleth maps, where statistically significant clusters (hotspots/coldspots) are denoted in red/blue. 
- While this graphical display is ideally suited for purely spatial data, spatiotemporal and multivariate representations are problematic. How do we plot study areas with large number of spatial units? 

---

<!-- _backgroundColor: #31708f -->
<!-- _color: white-->
<!-- _footer: '' -->

<!-- ![bg left:33% width:100%](choro.PNG) -->
## <!-- fit --> II. The software :floppy_disk:


---

# VASA - Visual Analytics for Spatial Associations 

* FOSS written on Python 
* Ideally suited for exploratory spatial analysis on spatio-temporal areal data (e.g. Census units). Works with vector data.
* Offers several utility functions for spatial aggregation and visualizations

---

<!-- ![bg right:33% fit](https://avatars.githubusercontent.com/u/81709620?s=400&u=721b7804c376458082fae5b1998fd704794bc707&v=4) -->

![bg width:50% vertical](https://i.imgur.com/bwCU3Nw.png)
![bg](https://fakeimg.pl/800x600/ffffff/fff/?text=B)
![bg](https://fakeimg.pl/800x600/ffffff/fff/?text=C)
![bg width: 20%](https://test.pypi.org/static/images/logo-large.6bdbb439.svg)

* Check out the [Github repo](https://github.com/move-ucsb/VASA) 
* Navigate example [notebooks](https://github.com/move-ucsb/VASA/tree/main/notebooks)
* Install via pip (currently on [test](https://test.pypi.org/project/VASA/), alpha version)
```terminal
pip install -i https://test.pypi.org/simple/VASA
```

---

<!-- _backgroundColor: #31708f -->
<!-- _color: white-->
<!-- _footer: '' -->

<!-- ![bg left:33% width:100%](choro.PNG) -->
## <!-- fit --> III. The Case Study :national_park:

---

# Assessing COVID-19 induced mobility in USA in 2020 

* 2 data sources: *SafeGraph* (Open) & *Cuebiq* (Proprietary)
* Approximately 3,200 counties over the period of 2020 (daily data)

![](https://i.imgur.com/8jN1sX6.png)

---

# General Workflow

0. Import data and clean
1. Create a VASA Object
2. Aggregate by spatial units
3. Downsample to Week
4. Calculate Local Moran's $I$ for each week and county
5. Map *recency* | *consistency* | *week number* to visual variables 
6. Plot on a graphic display

<!-- ---

![bg vertical](https://fakeimg.pl/800x600/ffffff/fff/?text=B)
![bg](https://fakeimg.pl/800x600/ffffff/fff/?text=C)
![bg](https://fakeimg.pl/800x600/ffffff/fff/?text=C)
![bg width:30%](https://i.imgur.com/P6gnw2Z.png)

# Import data and clean

```python
import os
import pandas as pd
import geopandas as gpd
from VASA import VASA

counties_map = gpd.read_file("../test data/counties.geojson") \
    .set_crs(epsg=4326) \
    .to_crs("+proj=aea +lat_1=20 +lat_2=60 +lat_0=40 +lon_0=-96 +x_0=0 +y_0=0 +datum=NAD83 +units=m +no_defs")

# filter out to only continental us
counties_map = counties_map[[(state != '02' and state != '15' and int(state) < 60) for state in counties_map.STATEFP]]

# import the data file:
data = pd.read_csv("../test data/counties.csv")

# need to ensure that these GEOID's match the ones in our GeoDataFrame - add leading zeros
data.fips = [f"{d:05}" for d in data.fips]

data.head()
``` -->


---

# Create a VASA Object

```python
v = VASA(
    df=data,
    gdf=counties_map,
    group_summary=lambda g: g[:2], # state level
    df_group_col="fips",
    gdf_group_col="GEOID",
    date_col="ds_m4",
    temp_res="week",
    date_format="%Y-%m-%d"
).group()
```

---

<!-- # Impute missing values

<mark style="background: #5ec3ae; color: white"> Caution! VASA is very finicky about missing values!</mark>

```python
v.pct_full_missing()
```
> array([0.03217503])

```python
v.fill_missing()
```
> <VASA.vasa.VASA at 0x7f844422e250>

--- -->

# Build Spatial Weights Matrix 

Since VASA utilizes Moran's $I$, we need to construct spatial weights matrix (SWM). There are different methods to create SWM (Rook's, Queen's, kNN, etc.). Please refer to [PySAL docs](https://pysal.org/libpysal/generated/libpysal.weights.W.html) to learn more. 

```python
v.show_weights_connection(k=3, type="union")
```
![width:450px](https://i.imgur.com/VxLUkhl.png)

---

# Recency and Consistency Map 

> Better suited to study macro trends at aggregate (summary) level. 

```python
stacked.plot_both(a=2500, b=600)
```

<span style="color:red">hotspots</span>: high values co-located with other high values. 
<span style="color:blue">coldspots</span>: low values co-located with other low values. 
Clustering <mark>**consistency**</mark> is illustrated by **size**. 
Clustering <mark>**recency**</mark> is depicted by **color intensity**.

<!-- ![width:500px](https://i.imgur.com/bKV0bUk.png) -->

---

![bg 100%](https://i.imgur.com/jVIOeg0.png)



---

# Line-path scatter plot

> Better suited to study micro trends (object-level details). 

```python
from VASA.scatter import Scatter
sc = Scatter(v)
sc.plot(highlight="06") # this filters out CA
```
<!-- ![width:500px](https://i.imgur.com/bKV0bUk.png) -->

<span style="color:red">hotspots</span>:high values co-located with other high values. 
<span style="color:blue">coldspots</span>: low values co-located with other low values. 
<mark>Week number</mark> is mapped onto X-axis. 
<mark>Number of times classified as hs/cs</mark> is mapped onto Y-axis. 

---

# Social distancing in California

![](https://i.imgur.com/8hiQVOU.png)

<!-- ![bg vertical 75%](https://i.imgur.com/8hiQVOU.png)
![bg 75%](https://i.imgur.com/QpFXsHy.png) -->

---

# Social distancing in Florida

![](https://i.imgur.com/QpFXsHy.png)

--- 

<!-- _backgroundColor: #31708f -->
<!-- _color: white-->
<!-- _footer: '' -->

<!-- ![bg left:33% width:100%](choro.PNG) -->
## <!-- fit --> IV. Conclusion :rocket:

---

* Proposed methodology provides a robust framework for assessing spatiotemporal structure of the data (NOT only mobility!).
* VASA allows exploratory spatial data analysis at the object-level and summary-level, which makes comparison of multiple spatiotemporal series easy 
* VASA adds novel visual displays to traditional choropleth mapping prevalent in spatial econometrics
* *Planned: Adding more visual displays for micro-scale analysis on areal data (e.g. LA Census Block Groups ~ 25,000)*

---

<!-- _footer: '' -->
![bg](https://fakeimg.pl/1600x1200/5ec3ae/fff/?text=Questions?)

---

<!-- _class: lead -->
<!-- _footer: '' -->

# <!-- fit -->  Thank you for Zoom-listening! 

<div class="image123">
<center>
  <div class="container">
  <img class="img1" src="https://i.imgur.com/oWwqOix.jpg" width="150" />
  <p>Evgeny Noi</p>
  </div>
   <div class="container">
    <img class="img1" src="https://i.imgur.com/uTdQrko.jpg" width="150" />
    <p>Alex Rudolph</p>
  </div>
    <div class="container">
    <img class="img1" src="https://i.imgur.com/aC8fFlB.png" width="150" />
    <p>Somayeh Dodge</p>
  </div>
  </center>
</div>

<span><center>noi@ucsb.edu</span>
<span><center>geog-move@ucsb.edu</span>

<div class="image123">
<center>
  <div class="container">
    <img src="https://www.giscience.org/img/patrons.png" width="200" />
  </div>
  <div class="container"> 
    <img class="middle-img" src="https://move.geog.ucsb.edu/wp-content/uploads/2021/08/cropped-MOVE_final_nobackground.png"  width="200" />
  </div>
  <div class="container">
    <img src="https://i.imgur.com/YxCk4Qb.png" width="200" />
  </div>
  <div class="container">
  <img src="https://www.giscience.org/img/giscience_icon.png" width="200" />
  </div>
  <div class="container">
  <img src="https://www.nsf.gov/images/logos/NSF_4-Color_bitmap_Logo.png" width="70" />
  </div>
  <div class="container">
  <p style="font-size:20px">The authors gratefully acknowledge the support from the National Science Foundation Award BCS # 2043202 “CAREER: Modeling Movement and Behavior Responses to Environmental Disruptions”.</p>
  </div>
  </center>
</div>