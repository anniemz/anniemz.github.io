---
layout: project_page
title: 'Oakland Property Tax: Part 3 All things GIS'
image_path: /assets/img/projects/oaklandpart3.png

---

At this point, I had collected all the data including property tax values and addresses. My plan was to use the addresses to pull coordinate data for mapping using the 'Nominatim' package in 'geopy'. Unfortunately, I realized that would only give me point data which wouldn't make for a very visually interesting map. Instead, I hunted around for GIS data on the boundaries of each parcel. Lucikly, this dataset was readily available from the [UCLA geoportal](https://apps.gis.ucla.edu/geodata/dataset/california-statewide-parcel-boundaries) which has parcel boundaries for the entire state of California. 

<h1> PostGIS </h1>

The one wrinkle with the parcel boundary dataset from UCLA was that it was a geodatabase which was an unfamiliar format to me. A geodatabase, `.gdb`, is a file format created by a company called ESRI. They make ArcGIS, one of the main tools used in the GIS industry and one of the normal tools for working with a geodatabase. Since I don't have and wasn't going to pay for or learn ArcGIS, I hunted around for a way to get the geodatbase into a geojson or geopandas format. I landed on `ogr2ogr` from [GDAL](https://gdal.org/programs/ogr2ogr.html) and used that package to load the parcel boundary data into a PostGIS database. A PostGIS database is just a PostgreSQL database with another layer on top to support geographic objects like shape boundaries and coordinates. 

Once the data was in PostGIS, I thinned the database down to only parcels in Alameda County and I was off to the races. 

[**Next Part: Bringing it all together and mapping the data**](/dataprojects/oakland_property_tax_part4)
