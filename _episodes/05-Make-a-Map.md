---
title: "Plotting occurrence data from the query \"Download record counts of Peron's tree frog since 2018 in New South Wales by FrogID\""
start: true
teaching: 10
exercises: 10
questions:
- "How can I make a map using occurrence records from the ALA?"
objectives:
- "Be able to download occurrence records, including select columns of data"
- "Plot a shape file using geopandas"
- "Plot occurrence records from the ALA in context with a shape file"
keypoints:
- "Occurrence records can be downloaded by using queries from `atlas_counts()`, but using the function `atlas_occurrences` instead"
- "You can specify what columns of data you want from `atlas_occurrences()` by using the `fields` option"
- "Plotting the occurrence data on a map can be done with `galah-python`, `matplotlib` and `geopandas`"
---

# Download occurrence records 

Remember the query we built from Episode 2:

#### <u>Download record counts of Peron's tree frog since 2018 in New South Wales by FrogID</u>

```python
galah.atlas_counts(
    taxa="litoria peronii",
    filters=["year>=2018",
             "cl22=New South Wales",
             "dataResourceName=FrogID"]
)
```
```output
   totalRecords
0         27969
```

All we have to do to download occurrences is to change the function name `atlas_counts` to `atlas_occurrences`, but first, we need to provide an email registered with the ALA to `galah-python`:

```python
galah.galah_config(email="amanda.buyan@csiro.au")
galah.atlas_occurrences(
    taxa="litoria peronii",
    filters=["year>=2018",
             "cl22=New South Wales",
             "dataResourceName=FrogID"]
)
```
```output
       decimalLatitude  decimalLongitude             eventDate  ...                              recordID dataResourceName occurrenceStatus
0           -37.246800        149.375000  2020-12-27T00:00:00Z  ...  a5cd2fcd-5225-4d19-977c-b16ca5e8f1dd           FrogID          PRESENT
1           -37.089036        149.699526  2020-12-14T00:00:00Z  ...  eebde5ef-cac4-4897-af00-cb2e39a0684f           FrogID          PRESENT
2           -37.077693        149.874402  2018-01-06T00:00:00Z  ...  35340478-97c1-48a4-a463-991fe3a8daa0           FrogID          PRESENT
3           -37.077241        149.874787  2018-01-06T00:00:00Z  ...  a7abc9f3-362f-469e-9076-5b55a2447b69           FrogID          PRESENT
4           -37.070746        149.896011  2020-12-13T00:00:00Z  ...  1cc9dda8-f2d4-4f55-acf6-11c93b26da9e           FrogID          PRESENT
...                ...               ...                   ...  ...                                   ...              ...              ...
27642       -28.207514        153.442592  2018-11-15T00:00:00Z  ...  b094fed1-5bff-4df8-b556-cabd693c533a           FrogID          PRESENT
27643       -28.207472        153.442497  2018-11-15T00:00:00Z  ...  5cc24fbd-8c6b-4a76-9b28-fec76ee08f37           FrogID          PRESENT
27644       -28.207442        153.442328  2020-02-07T00:00:00Z  ...  61aa50a1-4c79-4fc3-b3ab-93538faa37b1           FrogID          PRESENT
27645       -28.207108        153.443021  2021-02-19T00:00:00Z  ...  0324b8d1-77b0-4bf3-9ec2-6ad9efff18f2           FrogID          PRESENT
27646       -28.186157        153.445556  2018-11-16T00:00:00Z  ...  bcf83a54-a900-4265-960a-9436356a7107           FrogID          PRESENT
```

All of this data for each occurrence record is great!  However, say you want to only get specific columns of the table, like `decimalLatitude`,`decimalLongitude` and `scientificName`.  You can specify column names in the `fields` argument of `atlas_occurrences`:

```python
galah.galah_config(email="amanda.buyan@csiro.au")
galah.atlas_occurrences(
    taxa="litoria peronii",
    filters=["year>=2018",
             "cl22=New South Wales",
             "dataResourceName=FrogID"],
    fields=["scientificName","decimalLatitude","decimalLongitude"]
)
```
```output
        scientificName  decimalLatitude  decimalLongitude
0      Litoria peronii       -33.989891        151.077801
1      Litoria peronii       -30.399200        149.770567
2      Litoria peronii       -33.714285        151.083234
3      Litoria peronii       -34.026709        150.592730
4      Litoria peronii       -33.746989        151.076461
...                ...              ...               ...
27642  Litoria peronii       -30.399331        149.771156
27643  Litoria peronii       -32.360494        152.389057
27644  Litoria peronii       -33.650044        150.806782
27645  Litoria peronii       -33.763595        150.968821
27646  Litoria peronii       -34.573658        150.801913
```

# Make a map of *Litoria peronii* occurrence records since 2018 in New South Wales

Now, we will make a map showing where all the occurrence records we downloaded are in New South Wales.  This is where the packages `matplotlib` and `geopandas` you installed at the beginning of the lesson will be used.  The shape file you will need for this exercise can be found [here](https://www.abs.gov.au/statistics/standards/australian-statistical-geography-standard-asgs-edition-3/jul2021-jun2026/access-and-downloads/digital-boundary-files/STE_2021_AUST_SHP_GDA94.zip).

```python
import galah
from matplotlib import pyplot as plt
import geopandas as gpd

# Get Peron's tree frog occurrences
frogs = galah.atlas_occurrences(
  taxa="litoria peronii",
  filters=["year>=2018",
           "cl22=New South Wales",
           "dataResourceName=FrogID"],
  fields=["scientificName","decimalLongitude","decimalLatitude"]
)

# Get Australian state and territory boundaries  
states = gpd.read_file("STE_2021_AUST_GDA94.shp")

# Change Coordinate Reference System (CRS) of the shape file and plot New South Wales 
states = states.to_crs(4326)
states[states["STE_NAME21"] == "New South Wales"].plot(edgecolor = "#5A5A5A", linewidth = 0.5, facecolor = "white", figsize = (24,10))
```

![](../fig/states_map.png)

```python
# Add occurrence records to the map
ax = states[states["STE_NAME21"] == "New South Wales"].plot(edgecolor = "#5A5A5A", linewidth = 0.5, facecolor = "white", figsize = (24,10))
plt.scatter(frogs['decimalLongitude'],frogs['decimalLatitude'], c = "#6fab3f", alpha = 0.5)
```

![](../fig/frog_occurrences.png)