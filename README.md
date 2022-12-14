# NYC-Open-Data

## Packages
* matplotlib
* pandas
* geopandas
* descartes
* sodapy
* shapely


## Datasets
Shapefiles are accessible via [Berkeley Library Geodata](https://geodata.lib.berkeley.edu/)

NYC data are accessible via [NYC Open Data](https://opendata.cityofnewyork.us/data/)

## Plot NYC Buildings
To retrieve NYC buildings, download [NYC building shapefile](https://dev.socrata.com/foundry/data.cityofnewyork.us/e42i-6hpb) from [Berkeley Library Geodata](https://geodata.lib.berkeley.edu/). Buildings are stored as polygons and filesize is extremely large, slowing download and plotting. Streets are a recommended alternative background.
```
buildings = gpd.read_file('./shapefile/geo_export_426d25b7-267f-47a3-88cc-132bf267b23d.shp')
fig, ax = plt.subplots(figsize = (15, 15))
buildings.plot(ax=ax)
```
![download](https://user-images.githubusercontent.com/79494397/207496710-7d4402a9-b10b-4e64-af7f-beab311a19b0.png)

## Plot NYC Streets
To retrieve NYC streets, download [NYC roads shapefile](https://geodata.lib.berkeley.edu/catalog/nyu-2451-34499) from [Berkeley Library Geodata](https://geodata.lib.berkeley.edu/).  Roads are stored as lines and filesize is significantly smaller, ideal for plotting in background.
```
roads = gpd.read_file('./shapefile/geo_export_426d25b7-267f-47a3-88cc-132bf267b23d.shp')
fig, ax = plt.subplots(figsize = (15, 15))
roads.plot(ax=ax)
```
![download](https://user-images.githubusercontent.com/79494397/207496905-ccb6f122-4b0a-4d4f-b4ee-f6f3dd36910c.png)

## Plot NYC Subway Routes and Stops
To retrieve NYC subway routes and stops, download [NYC subway routes shapefile](https://geodata.lib.berkeley.edu/catalog/nyu-2451-34758) and [NYC subway stops shapefile](https://geodata.lib.berkeley.edu/catalog/nyu-2451-34503) from [Berkeley Library Geodata](https://geodata.lib.berkeley.edu/).
```
subway_stops = gpd.read_file('./shapefile/nyu_2451_34503.shp')
subway_routes = gpd.read_file('./shapefile/nyu_2451_34758.shp')

fig, ax = plt.subplots(figsize = (15, 15))
subway_stops.plot(ax=ax, alpha=0.5, color='purple', markersize=15)
subway_routes.plot(ax=ax, alpha=0.5)
```
![download](https://user-images.githubusercontent.com/79494397/207500145-584569cf-8230-4c48-a0d8-a2bdcf762846.png)

## Configure Arrest Location with Point Geometry
To retrieve NYC arrest location data, access [year-to-date NYPD Arrest Data](https://data.cityofnewyork.us/Public-Safety/NYPD-Arrest-Data-Year-to-Date-/uip8-fykc) from [NYC Open Data](https://opendata.cityofnewyork.us/data/) using the [SODA API](https://dev.socrata.com/foundry/data.cityofnewyork.us/uip8-fykc) with the sodapy package.

Fetch data:
```
client = Socrata("data.cityofnewyork.us", None)
query = client.get("uip8-fykc", limit=2000) # Fetch no more than 2000 entries
arrests = pd.DataFrame.from_records(query)
```
Using latitude and longitude columns, extract point values for new geometry feature:
```
longitude = pd.to_numeric(arrests['longitude'])
latitude = pd.to_numeric(arrests['latitude'])
crs = {'init':'epsg:4326'}
geometry = [Point(xy) for xy in zip(longitude,latitude)]
geo_arrests = gpd.GeoDataFrame(arrests, crs=crs, geometry=geometry)
```
Plot new GeoDataFrame:
```
fig, ax = plt.subplots(figsize = (15, 15))
geo_arrests.plot(ax=ax, markersize=20, alpha=0.5, color='red', marker='x')
```
![download](https://user-images.githubusercontent.com/79494397/207506899-c145db7a-8ec4-4663-8225-fed47e18167d.png)

## Combined Plot of NYC Backgrounds and Arrests
**Plot arrests with subway routes:**
```
fig = plt.figure(figsize=(15, 4))
ax1 = fig.add_subplot(1, 3, 1)
ax2 = fig.add_subplot(1, 3, 2)
ax3 = fig.add_subplot(1, 3, 3)
subway_routes.plot(ax=ax1, alpha=0.25, markersize=5, color='blue')
geo_arrests.plot(ax=ax2, alpha=0.25, markersize=5, color='red', marker='x')
subway_routes.plot(ax=ax3, alpha=0.25, markersize=5, color='blue')
geo_arrests.plot(ax=ax3, alpha=0.25, markersize=5, color='red', marker='x')
```
![download](https://user-images.githubusercontent.com/79494397/207507140-6edb2c32-49b7-4ea4-a621-bdff3a3ad350.png)

**Plot arrests with NYC buildings and subway stops:**
```
fig, ax = plt.subplots(figsize = (15, 15))

# Plot shapefile backgrounds
buildings.plot(ax=ax)
subway.plot(ax=ax, alpha=0.6, markersize=15, color='teal')

# Add point geometry from arrest data
geo_arrests.plot(ax=ax, markersize=10, alpha=0.6, color='purple', marker='x', label='Arrest Site')
```
![download](https://user-images.githubusercontent.com/79494397/207507377-a3eec9f0-e69b-4daf-8e16-7e0488888403.png)

**Plot arrests with NYC roads, subway routes, and subway stops:**
```
fig, ax = plt.subplots(figsize = (15, 15))

# Plot shapefile backgrounds
roads.plot(ax=ax, markersize=1, alpha=0.2, color='grey')
subway_routes.plot(ax=ax, alpha=0.15, markersize=5, color='blue')
subway_stops.plot(ax=ax, alpha=0.3, markersize=10, color='blue', marker='o')

# Plot point geometry from arrest data
geo_arrests.plot(ax=ax, markersize=10, alpha=0.6, color='red', marker='x', label='Arrest Site')
```
![download](https://user-images.githubusercontent.com/79494397/207507556-36074c52-bc1f-4a60-90ab-6cf0939d04ec.png)


