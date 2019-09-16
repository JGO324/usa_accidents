# Death by accidents in USA 2017 projects

## Fatality facts 2017 State by state.
This data is extracted from Insurance Institute for Highway Safety (IIHS).
Analysis of fatality vehicle death rates per state and per vehicle miles traveled in USA relative to the population.
On the above map, you can visualize the adminitrative data of all 50 states and the district of Columbia.
States that are in dark red, are state that has more deaths per 100,000 people.
The deaths per 100 million vehicle traveled range from 0.58 in Massachusetts to 1.80 in South Carolina and Death per 100,000 people range from 4.5 District of Columbia to 23.1 in Mississippi. Total crashes of motor vehicle in the United States in 2017 is 34,247 and total deaths is 37,133. Total population of USA is 325,719,178.

## Data

1. Administrative data from [Insurance Institute for Highway Safety (IIHS)](https://www.iihs.org/topics/fatality-statistics/detail/state-by-state#crash-types) Fatal crash totals table.
3. USA States and countries scale 1:10m from [NationalEarth website](http://www.naturalearthdata.com/).

## Data preparation and visualization
1. Spatialite database was used to store and perform queries.
2. QGIS is used to visualize the data and then export the data to geojson file format. This resulted in USA state centroids, USA state polygons and the administrative data in csv format. The geojson files are exported in WGS84.
3. Then the geojson files are uploaded to Carto.
4. In Carto, spatial join are used to join the "USA states"/"USA states centroids" with the administrative data to create new table with geometry.
This new data are called as follow:
  - *Deaths per population (Point geometry type)*
  - *Deaths per vehicle traveled (Polygon geometry type)*
5. Deaths per vehicle traveled are visualized as state in red color scheme of 7 buckets and Deaths per population are visualized as points on each state. To visualize each point with different size, I used the Deaths per 100 million people data.
6. The points are shown on zoom level greater than 4 and the labels of the states disappear on level 6 or greater. This can be archive with the carto css.
7. finally, the projection was changed to EPSG: 5070 (NAD83/Conus Albers)

## Queries
````SQL
/*
This query is a spatial join that join the "USA states centroids" with
the "USA deaths by accidents" table.
*/
SELECT
x.cartodb_id,
st_transform(x.the_geom,5070) as the_geom_webmercator,/*transform the geometry to EPSG: 5070*/
b.state,
b.population,
b.vehicle_miles_traveled_millions_ as vehicle_miles_traveled_millions,
b.deaths,
b.deaths_per_100000_population
FROM jgo324.usa_states_centroid as x
inner join jgo324.usa_deaths_by_accidents as b
on x.name=b.state


/*
This query is a spatial join that join the "USA states" with
the "USA deaths by accidents" table.
*/
SELECT
ST_Transform(x.the_geom,5070)AS the_geom_webmercator,/*transform the geometry to EPSG: 5070*/
x.cartodb_id,
b.state,
b.population,
b.deaths,
b.fatal_crashes,
b.deaths_per_100_million_vehicle_miles_traveled
FROM jgo324.usa_states as x
inner join jgo324.usa_deaths_by_accidents as b
on x.name=b.state
````
