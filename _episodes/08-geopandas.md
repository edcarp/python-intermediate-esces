---
title: A brief introduction to geospatial data
teaching: 30
exercises: 45
questions:
    - "What can I do with geospatial data in Python?"
    - "How can I visualise and analyse this data?"
objectives:
    - "Import the Geopandas module to analyse latitude / longitude data."
    - "Use Geopandas and Geoplot to help with visualisation."
keypoints:
    - "Geopandas is the key module to help deal with geospatial data."
    - "Using Geopandas and Geoplot we can create publication / web-ready maps."
---


## Geospatial Data

Often in the Environmental Sciences, we need to deal with geospatial data. 
This is normally presented as latitude and longitude (either as decimal degrees or
as degrees/minutes/seconds), but can be presented in other formats (e.g. OSGB for UK 
Grid References).

A full discussion of geospatial vector data is beyond the scope of this
episode - if you need more background please see [this Carpentries Incubator
lesson](https://carpentries-incubator.github.io/geospatial-python/). Instead, we will
highlight some useful tasks that can achieved with some key python libraries.

We'll be using the data about buoys again.

~~~
import pandas as pd
buoys = pd.read_csv("data/buoy_data.csv",
                         keep_default_na=False, na_values=[""])
~~~
{: .language-python}

We can see that the dataset has a `latitude` and `longitude`. Let's subset this data, along with the names.

~~~
locations = buoys[["Name", "latitude", "longitude"]]
~~~
{: .language-python}

To be able to deal with geospatial data, we need a python package that doesn't come
included with the Conda distribution we're using. We can install the additional packages
we need directly within a Notebook:

~~~
conda install geopandas -c conda-forge
conda install geoplot -c conda-forge
~~~
{: .language-python}

These might take several minutes to run. Once they've been installed, we can import them:

~~~
import geopandas as gpd
import geoplot as gplt
~~~
{: .language-python}

> ## Conda environments
> We're now at the stage where you might find it useful to have different python _environments_ for specific 
> tasks. When you open Anaconda Navigator, it will, by default, be running in your `base` environment.
> However, you can create new environments via the Environments tab in the left-hand menu. Each environment
> can have different packages (or different versions of packages), different versions of python, etc - and 
> different packages can be installed via the Environments tab. However, note that individual Notebooks are _not_
> associated with specific environments - they are associated with the current _active_ environment. A full 
> introduction to Conda environments can be found at [https://carpentries-incubator.github.io/introduction-to-conda-for-data-scientists/](https://carpentries-incubator.github.io/introduction-to-conda-for-data-scientists/)
{: .callout}

In our `locations` DataFrame, latitude and longitude are of type float:

~~~
locations.dtypes
~~~
{: .language-python}

~~~
Name          object
latitude     float64
longitude    float64
dtype: object
~~~
{: .output}

We need to convert the latitude and longitude to _geometry_ data using Geopandas:

~~~
buoys_geo = gpd.GeoDataFrame(
    locations, geometry=gpd.points_from_xy(locations.longitude, locations.latitude), crs="EPSG:4326"
)
~~~
{: .language-python}

The value we've given to the `crs` argument specifies that the data is latitude and longitude, rather
than any other coordinate system. We can now see that the `buoys_geo` DataFrame contains a new column, `geometry`, 
which also has type `geometry`.

So, what can we do with this data type?

## Calculating distances

Calculating distances often involves some error if we need to convert between different coordinate types.
Geopandas includes a `distance` function, but to calculate a distance in meters, we need to either project
them in a local coordinate system to approximate the distance with a good precision, or use the
Haversine equation (which is more accurate, but not implemented in Geopandas). In our case, we can project
the data to the UK National Grid. The resulting distances will then be in metres:

~~~
buoys_geo.to_crs(epsg=27700,inplace=True)
buoys_geo["geometry"].distance(buoys_geo.iloc[0,3])
~~~
{: .language-python}

Notice the `espg=27700` argument - this is the ESPG code for the UK National Grid. We have then calculated the
distance of every point relative to the first point in the Series (Beryl A).

For the rest of the lesson, we need to consider the data back in Latitude / Longitude format, so let's revert it back:

~~~
buoys_geo.to_crs(epsg="4326",inplace=True)
~~~
{: .language-python}

## Geospatial polygons

In our case, the geospatial data are all individual points. However, geospatial data can also deal with polygons. Let's load in data about Scottish Local Authority Boundaries:

~~~
scotland = gpd.read_file("data/scotland.geojson")
~~~
{: .language-python}

We can immediately plot this:

~~~
scotland.plot()
~~~
{: .language-python}

We can see it looks like Scotland! We can look at the `shape` of the DataFrame to see that it has 32 rows - this is the number of Local Authorities in Scotland, and 5 columns. 

We can find the "centroid" point of each Polygon - we can even plot this if we want an abstract map of Scotland!

~~~
scotland.centroid.plot()
~~~
{: .language-python}

We can also find the boundary length of each polygon. If we create add this to the DataFrame, then we can sort the resulting DataFrame
and see which Council area has the shortest boundary:

~~~
scotland["lengths"] = scotland.length
scotland.sort_values("lengths")
~~~
{: .language-python}

Now, let's look at a different geospatial file: the boundaries of the Cairngorms National Park, one of the
National Parks in Scotland, and we can plot it

~~~
# Notice this is a different file format to the geojson file we used for the Scottish Council Boundaries data
# The corresponding `shx` file (with the same filename) also needs to be in the same directory  
cairngorms =  gpd.read_file("data/cairngorms.shp")
cairngorms.plot()
~~~
{: .language-python}

> ## Challenge: distances
> This dataset contains the length of the boundary. Double check that it is correct
{: .challenge}

One advantage of using geospatial data is seeing how things overlap. Geopandas contains a `overlap` method to
find objects which have some (but not all) points in common. There is also a similar `intersects` method.

The `overlap` method can compare a single obect with a Series of objects, so let's see which Scottish areas overlap with
the Cairngorms National Park:

~~~
scotland.overlaps(cairngorms.iloc[0].geometry)
~~~
{: .language-python}

> ## Challenge: overlaps
> 1. Subset the Scotland dataset to show only the rows which overlap with the Cairngorms
> 2. Look in the Geopandas documentation (https://geopandas.org/en/stable/index.html) for
>    the `disjoint` method. What do you think it will return when you run it in the way that we 
>    ran `overlap`? Try it - did you get the expected result? Can you plot this?
{: .challenge}

> ## Challenge: plotting
> We've already seen that we plot GeoDataFrames. We can pass these to Matplotlib subplots in the
> same way as any other figure. Can you plot the Scotland and Cairngorms GeoDataFrames on the same
> axes, and customise the plot to highlight the Cairngorms data in some way?
> > ## Solution
> > import matplotlib.pyplot as plt
> >
> > ~~~
> > figure, ax = plt.subplots()
> > scotland.plot(ax=ax)
> > cairngorms.plot(ax=ax, color="green")
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

Finally, Geopandas has a method to show geospatial data over an interactive map:

~~~
cairngorms.explore(style_kwds={"fillColor":"lime"})
~~~
{: .language-python}

## Back to our buoy data 

Our buoy data is based around the UK. Geopandas includes some very low resolution maps which we can use to plot our geospatial data on

~~~
world = gpd.read_file(gpd.datasets.get_path("naturalearth_lowres"))
ax = world.clip([-15, 50, 9, 61]).plot(color="white", edgecolor="black")
buoys_geo.plot(ax=ax, color="blue")
~~~
{: .language-python}

We use the `clip()` function to limit the bounds of the map to the most useful area for our needs.

What about if we want a higher quality map? There are several ways of achieving this. We've already seen
that the `explore()` function gives us a way of generating an interactive map, but we can also use the Geoplot package,
or Geopandas directly, to create maps. We'll just use Geopandas, but Geoplot can give some more fine-grained control if you
require it.

First, we need to import a basemap to plot buoy points onto.

~~~
north_atlantic = gpd.read_file("data/north_atlantic.geojson")
~~~
{: .language-python}

> ## Where to find data
> One challenge with mapping is often to find appropriate data.
> This file came from the NUTS dataset: [https://marineregions.org/gazetteer.php?p=details&id=1912](https://marineregions.org/gazetteer.php?p=details&id=1912). Another useful source of
> European data is the EU ([https://ec.europa.eu/eurostat/web/gisco/geodata/reference-data/administrative-units-statistical-units/nuts#nuts21](https://ec.europa.eu/eurostat/web/gisco/geodata/reference-data/administrative-units-statistical-units/nuts#nuts21)),
> while the Cairngorms data we looked at earlier came from the UK Government geospatial data catalogue
> ([https://www.data.gov.uk/dataset/8a00dbd7-e8f2-40e0-bcba-da2067d1e386/cairngorms-national-park-designated-boundary](https://www.data.gov.uk/dataset/8a00dbd7-e8f2-40e0-bcba-da2067d1e386/cairngorms-national-park-designated-boundary)), and the
> Scottish data came from the Scottish Government ([https://data.spatialhub.scot/dataset/local_authority_boundaries-is/resource/d24c5735-0f1c-4819-a6bd-dbfeb93bd8e4](https://data.spatialhub.scot/dataset/local_authority_boundaries-is/resource/d24c5735-0f1c-4819-a6bd-dbfeb93bd8e4)) 
{: .callout}

We can then plot the location of the buoys, and save the figure as we saw earlier. Although we could use the same technique as in the previous example (where we set the map as the axis and plotted the buoy positions on this object), here we're showing we can also use Matplotlib subplots. This will allow us more control over the subsequent plot. However, subplots aren't suppoorted directly via Pandas or Geopandas, so we now need to import Matplotlib

~~~
import matplotlib.pyplot as plt

fig, ax = plt.subplots()
north_atlantic.plot(ax=ax)
buoys_geo.plot(ax=ax, color="red")
plt.savefig("b.png")
~~~
{: .language-python}

> ## Challenge - annotation
> Earlier, we saw how to annotate figures. Can you annotate this map with the name of the buoy each dot represents?
{: .challenge}

> ## Challenge
> The North Atlantic dataset is comprised of lots of individual areas, which our buoys are all in 1 corner of. Can you:
> - use the buoy data to find the extent of the data we need to plot
> - subset the North Atlantic GeoDataFrame to only include the areas that will include the buoy data
> - plot the data
> - if you have time, investigate how you might customise the plot
{: .challenge}


{% include links.md %}
