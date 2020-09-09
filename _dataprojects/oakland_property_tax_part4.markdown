---
layout: project_page
title: 'Oakland Property Tax: Part 4 Making Beautiful Maps'
image_path: /assets/img/projects/oaklandpart4.png

---

Gathering all the data for this project was the hard part. Using `geopandas` and to clean it and putting `folium` to work to map it was the fun part!


<h1> Geopandas </h1>
Once I had the parcel boundary shape data in a PostGIS database, it was easy enough to read it into a geopandas dataframe:

`conn_string = "host="+ PGHOST +" port="+ "5432" +" dbname="+ PGDATABASE +" user=" + PGUSER  +" password="+ PGPASSWORD`
`conn=psycopg2.connect(conn_string)`

`geodf_3d= geopandas.read_postgis('SELECT * FROM alameda_parcels;', conn, geom_col='shape')`

from there, I also pulled in my scraped parcel & address data file: 

`parcel_df= pd.read_json('data/parcel_data_combined.json').transpose()`

and then I was able to merge them together with a simple join: 

	parcel_geodf = geodf_3d.merge(parcel_df,
	                              how = 'inner',
	                              left_on='parno',
	                              right_on='Parcel_number')

I did some simple filtering on the data to only look at single-family type residences and removed the outliers (z-score>3).

From there, I had most of my data ready for plotting. 

<h1> Folium </h1>
There are a ton of great packages for plotting spatial data in python. I chose `folium` because it was easy to get started with and works well with `geopandas`. 

To use folium, I just created a map: 
	`my_map = folium.Map(location =[37.818158, -122.248614],
	                    tiles = "Stamen Terrain",
	                    zoom_start = 15)`

It's then easy to add geopandas data as layers to create Chloropleth maps. `folium` has a built in plugin for creating Chlorpleth maps, but since I made a custom colormap and color function, I added the data to the map using the `GeoJson` class. 

	`parcel_geo = folium.features.GeoJson(
	    parcel_geodf,
	    style_function=style_function,
	    control=False,
	    highlight_function=highlight_function,
	    tooltip=folium.features.GeoJsonTooltip(
	        fields=['parno','2020_PropertyTax','Address', 'Descriptio'],
	        aliases=['Parcel Number: ','Assesed Property Value', 'Address', 'Description'],
	        style=("background-color: white; color: #333333; font-family: arial; font-size: 12px; padding: 10px;")
	    )
	)`

with custom styling & coloring:

	style_function = lambda x: {'fillColor': my_color_function(x['properties']['2020_PropertyTax_number']),
	                            'color':'#000000',
	                            'fillOpacity': 0.6,
	                            'weight': 0.1}

Just a few other steps to add the layer to the map: 
	`my_map.add_child(parcel_geo)`
	`folium.LayerControl().add_to(my_map)`
	`colormap.add_to(my_map)`



<embed type="text/html" src="/assets/static/property_tax_all_parcels.html" width="100%" height="500" >


<h1> Takeaways </h1>

Plotting all of this data confirmed my suspicions that Oakland's assesed housing values are *very* low. Many, many houses are valued below $600,000, many even under $100,000. It's interesting too to observe that lots of blocks have houses in a range between $50,000 and $1,000,000+. 

While it's really fun for a Zillow-addicted, real estate junkie like me to be able to look at this data block by block in Oakland, it's hard to draw any larger conclusions or pull out the patterns. To solve this, I decided to average the data by zip code and compare assesed property values to market rate by zip code. I then computed a simple ratio between the two to see if any zipcodes are getting a better "deal". Not surpisingly, the median assesed value is less than half that of the market value in all zip codes. While certain zips (looking at you 94705 and 94605) are towards the lower end of the spectrum, the ratios are all generally clustered between 30 and 45%. 


<embed type="text/html" src="/assets/static/property_tax_zip.html" width="100%" height="500" >