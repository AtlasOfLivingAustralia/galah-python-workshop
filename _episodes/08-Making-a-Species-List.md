---
title: "Creating Species Lists and Filtering by Threatened Species"
start: true
teaching: 15
exercises: 5
questions:
- "What is a species list?"
- "How do I make a species list"
- "How do I cross-reference a threatened species list with my own species list?"
objectives:
- "Understand what a species list is and its purpose"
- "Generate a list of species from a shapefile"
- "Cross reference a list of species with a threatened species list"
keypoints:
- "Species lists are useful to know which species are in a particular area, or for reporting"
- "You can generate species lists from any specified rank"
- "Generating a species list with counts takes little time and few lines of code"
---

#### *Note: based on* https://labs.ala.org.au/posts/2024-02-12_species-lists/

# What is a species list?  Why do they matter?

Knowing what species inhabit an area is important for conservation and ecosystem management. In particular, it can help us find out how many known species are in a given area, whether each species is common or rare, and whether any species are threatened or endangered.

# Download a list of species

There are two ways to narrow a download query to return information for a specific region:

- Using fields available in galah-python (downloaded from the ALA)
- Using a shapefile

The method you choose depends on whether the region you wish to return species for is already within galah-python, or whether you require a list for a more specific area defined by a shapefile.

#### Searching for fields available in the ALA using {galah-python}

To find which fields and layers exist within galah-python to help us narrow our query, we can use the `search_all()` function with the text “Local Government Areas”.

```python
import galah
galah.search_all(fields="Local Government Areas")
```
```output
          id                                        description    type link
0     cl959  Local Government Areas 2011 Local Government A...  layers     
1   cl11170  Local Government Areas 2023 Local Government A...  layers     
2      cl23  Local Government Areas 2012 deprecated LGA Bou...  layers     
3   cl10923  Local Government Areas PSMA 2018 PSMA Local Go...  layers     
4  cl110923  Local Government Areas PSMA 2018 PSMA Local Go...  layers  
```

The field `cl11170` appears to contain the most recent data (from 2023). We can preview what values are within the field `cl11170` using `show_values()`.

```python
galah.show_values(field="cl11170")
```
```output
       field            category
0    cl11170  Unincorporated ACT
1    cl11170            Brisbane
2    cl11170     Greater Geelong
3    cl11170      East Gippsland
4    cl11170         Moreton Bay
..       ...                 ...
542  cl11170    Peppermint Grove
543  cl11170         Wujal Wujal
544  cl11170          Woorabinda
545  cl11170           Cherbourg
546  cl11170             Belyuen
```

There are lots Local Government Areas! To check that Yass Valley is included, we can do another text search for values that match “Yass Valley”.

```python
galah.search_values(field="cl11170", value = "Yass Valley")
```
```output
     field     category
0  cl11170  Yass Valley
```

Now, we can use `atlas_species()` to return a species list, and use the filters argument to narrow our query. To add the observation count for each species, add `counts=True`.

```python
species_yass = galah.atlas_species(
  filters=["cl11170=Yass Valley", "year=2023"],
  counts=True
)
species_yass
```
```output
                                                Species                       Species Name  ...           Vernacular Name Number of records
0     https://biodiversity.org.au/afd/taxa/5291343e-...                 Gymnorhina tibicen  ...         Australian Magpie              1237
1     https://biodiversity.org.au/afd/taxa/2c33a1fd-...         Cacatua (Cacatua) galerita  ...  Sulphur-crested Cockatoo               913
2     https://biodiversity.org.au/afd/taxa/9b4ad548-...              Eolophus roseicapilla  ...                     Galah               833
3     https://biodiversity.org.au/afd/taxa/ae56080e-...          Malurus (Malurus) cyaneus  ...         Superb Fairy-wren               830
4     https://biodiversity.org.au/afd/taxa/5e0f496f-...  Platycercus (Platycercus) elegans  ...           Crimson Rosella               806
...                                                 ...                                ...  ...                       ...               ...
1179  https://id.biodiversity.org.au/taxon/apni/5173...          Alternanthera denticulata  ...            Lesser Joyweed                 1
1180  https://id.biodiversity.org.au/taxon/apni/5173...           Arthropodium milleflorum  ...         Pale Vanilla-lily                 1
1181  https://id.biodiversity.org.au/taxon/apni/5173...                Eucalyptus camphora  ...        Broad-leaved Sally                 1
1182  https://id.biodiversity.org.au/taxon/apni/5174...                   Lactuca serriola  ...           Prickly Lettuce                 1
1183  https://id.biodiversity.org.au/taxon/ausmoss/1...                 Thuidiopsis sparsa  ...                 Weft Moss                 1
```

#### Using a shapefile to download a species list

To retrieve the spatial outline of Yass Valley, let’s download the latest Local Government Areas data from the [Australian Bureau of Statistics Digital Boundary files page](https://www.abs.gov.au/statistics/standards/australian-statistical-geography-standard-asgs-edition-3/jul2021-jun2026/access-and-downloads/digital-boundary-files). Find “Local Government Areas - 2024 - Shapefile” and click “Download ZIP”. Save the zip folder in your current directory and unzip it.

```python
import geopandas as gpd
LGAs_2024 = gpd.read_file("LGA_2024_AUST_GDA2020.shp")
LGAs_2024
```
```output
    LGA_CODE24                             LGA_NAME24  ...                                            geometry
0        10050                                 Albury  ...  POLYGON ((146.86566 -36.07292, 146.86512 -36.0...
1        10180                               Armidale  ...  POLYGON ((152.38816 -30.52639, 152.38812 -30.5...
2        10250                                Ballina  ...  MULTIPOLYGON (((153.57106 -28.87381, 153.57106...
3        10300                              Balranald  ...  POLYGON ((143.00433 -33.78164, 143.01538 -33.7...
4        10470                               Bathurst  ...  POLYGON ((149.84877 -33.52784, 149.84864 -33.5...
..         ...                                    ...  ...                                                ...
561      89799  Migratory - Offshore - Shipping (ACT)  ...                                               None
562      99399            Unincorp. Other Territories  ...  MULTIPOLYGON (((167.94747 -29.12757, 167.94748...
563      99499                  No usual address (OT)  ...                                               None
564      99799   Migratory - Offshore - Shipping (OT)  ...                                               None
565      ZZZZZ                      Outside Australia  ...                                               None
```

Now let’s use `to_crs()` to set the Coordinate Reference System (CRS) of our shapefile to EPSG:4326 (the standard used in cartography and GPS, also known as WGS84) so that it matches the projection of our data from the ALA.

```python
LGAs_2024 = LGAs_2024.to_crs(4326)
```

Next we can filter our shapefile to only Yass Valley. The column `LGA_NAME24` contains area names, and we can filter our `Dataframe` to only rows where `LGA_NAME24` is equal to `Yass Valley`. We are left with a single polygon shape of Yass Valley.

```python
yass_valley = LGAs_2024[LGAs_2024['LGA_NAME24'] == "Yass Valley"]
yass_valley["geometry"]
```
```output
127    POLYGON ((148.58152 -34.80679, 148.58153 -34.8...
Name: geometry, dtype: geometry
```

Now that `yass_valley` contains our LGA shape, we can build our query. We can use `atlas_species()` to return a species list, passing our shape to the `polygon` argument of `atlas_species()` and specifying the year to `filters`. To add the observation count for each species, add `counts=True`.

---
**NOTE**

For now, `galah-python` automatically simplifies your polygon.  This is due to the polygon being added to the URL for the query.  If the URL is too long, your query won't work.  In the future, we will work to include more complicated spatial polygons as a feature.

---

```python
species_yass = galah.atlas_species(
  polygon=yass_valley["geometry"][127],
  filters="year=2023",
  counts=True
  )
species_yass
```
```output
                                                Species                       Species Name  ...           Vernacular Name Number of records
0     https://biodiversity.org.au/afd/taxa/5291343e-...                 Gymnorhina tibicen  ...         Australian Magpie             11087
1     https://biodiversity.org.au/afd/taxa/ce17b284-...                Grallina cyanoleuca  ...               Magpie-lark              8060
2     https://biodiversity.org.au/afd/taxa/2c33a1fd-...         Cacatua (Cacatua) galerita  ...  Sulphur-crested Cockatoo              8041
3     https://biodiversity.org.au/afd/taxa/ae56080e-...          Malurus (Malurus) cyaneus  ...         Superb Fairy-wren              7493
4     https://biodiversity.org.au/afd/taxa/5e0f496f-...  Platycercus (Platycercus) elegans  ...           Crimson Rosella              7385
...                                                 ...                                ...  ...                       ...               ...
3225  https://id.biodiversity.org.au/taxon/apni/5174...                   Dahlia x pinnata  ...             Garden Dahlia                 1
3226  https://id.biodiversity.org.au/taxon/ausmoss/1...                Ceratodon purpureus  ...             Redshank Moss                 1
3227  https://id.biodiversity.org.au/taxon/ausmoss/1...                    Bryum argenteum  ...               Silver Moss                 1
3228  https://id.biodiversity.org.au/taxon/ausmoss/1...                 Thuidiopsis sparsa  ...                 Weft Moss                 1
3229  https://id.biodiversity.org.au/taxon/ausmoss/1...                  Breutelia affinis  ...          Common Breutelia                 1
```

# Cross-reference with threatened and sensitive species lists

Next we will compare our Yass valley species list with several state-wide conservation status lists of threatened and sensitive species. We can retrieve up-to-date lists of threatened and sensitive species in the Atlas of Living Australia.

```python
galah.search_all(lists="New South Wales")
```

Two lists are returned, and both appear relevant. With the help of some additional columns returned by `search_all()`—`listType`, `isAuthoritative` and `isThreatened` — we can learn more about which list suits our needs best. Although both lists are authoritative, only one list (`dr650`) contains threatened species whereas the other `dr487` contains sensitive species.

```output
  species_list_uid                                listName                                        description           listType  ... looseSearch isBIE  isSDS wkt
0            dr650   New South Wales : Conservation Status  Classification codes under the Threatened Spec...  CONSERVATION_LIST  ...        None  True  False    
1            dr487  New South Wales Sensitive Species List  The NSW Government [http://www.environment.nsw...     SENSITIVE_LIST  ...        None  True   True  
```

```python
galah.search_all(lists="New South Wales")[["species_list_uid", "listType", "isAuthoritative", "isThreatened"]]
```

```output
  species_list_uid           listType  isAuthoritative  isThreatened
0            dr650  CONSERVATION_LIST             True          True
1            dr487     SENSITIVE_LIST             True         False
```

We can return only species in Yass Valley on the New South Wales Conservation Status List (`dr650`) by adding the list ID `species_list_uid=dr650` to filters. Our query returns 58 species.

```python
yass_threatened = galah.atlas_species(
  polygon=yass_valley["geometry"][127],
  filters=["year=2023", "species_list_uid=dr650"],
  counts=True
  )
yass_threatened
```

```output
                                              Species                               Species Name  ...                 Vernacular Name Number of records
0   https://biodiversity.org.au/afd/taxa/6c646af8-...                   Callocephalon fimbriatum  ...              Gang-gang Cockatoo              1145
1   https://biodiversity.org.au/afd/taxa/46fca72f-...                       Polytelis swainsonii  ...                   Superb Parrot               960
2   https://biodiversity.org.au/afd/taxa/a3e5376b-...                Petroica (Petroica) boodang  ...                   Scarlet Robin               775
3   https://biodiversity.org.au/afd/taxa/5c1957dc-...           Gallinago (Gallinago) hardwickii  ...                  Latham's Snipe               436
4   https://biodiversity.org.au/afd/taxa/4788c00f-...                        Stictonetta naevosa  ...                   Freckled Duck               414
5   https://biodiversity.org.au/afd/taxa/924778ad-...                    Pyrrholaemus sagittatus  ...                Speckled Warbler               361
6   https://biodiversity.org.au/afd/taxa/8bf4b8b0-...       Daphoenositta (Neositta) chrysoptera  ...                 Varied Sittella               263
7   https://biodiversity.org.au/afd/taxa/b47a4ecd-...        Hieraaetus (Hieraaetus) morphnoides  ...                    Little Eagle               188
8   https://biodiversity.org.au/afd/taxa/fe74e658-...              Petroica (Littlera) phoenicea  ...                     Flame Robin               163
9   https://biodiversity.org.au/afd/taxa/dc420306-...        Haliaeetus (Pontoaetus) leucogaster  ...         White-bellied Sea-eagle               120
10  https://biodiversity.org.au/afd/taxa/c95af018-...          Epthianura (Epthianura) albifrons  ...              White-fronted Chat                89
11  https://biodiversity.org.au/afd/taxa/6e872b58-...      Stagonopleura (Stagonopleura) guttata  ...                Diamond Firetail                88
12  https://biodiversity.org.au/afd/taxa/0e647393-...                       Rostratula australis  ...        Australian Painted Snipe                74
13  https://biodiversity.org.au/afd/taxa/694a8fde-...                           Circus assimilis  ...                 Spotted Harrier                69
14  https://biodiversity.org.au/afd/taxa/655adcf7-...                           Oxyura australis  ...                Blue-billed Duck                68
15  https://biodiversity.org.au/afd/taxa/4cb195fd-...                          Lathamus discolor  ...                    Swift Parrot                62
16  https://biodiversity.org.au/afd/taxa/cd4e3b1b-...             Burhinus (Burhinus) grallarius  ...               Bush Stone-curlew                44
17  https://biodiversity.org.au/afd/taxa/d2e67121-...                    Aphelocephala leucopsis  ...              Southern Whiteface                40
18  https://id.biodiversity.org.au/taxon/apni/5128...                      Leucochrysum albicans  ...                    Hoary Sunray                34
19  https://biodiversity.org.au/afd/taxa/a2399fe3-...                Parvipsitta porphyrocephala  ...         Purple-crowned Lorikeet                31
20  https://biodiversity.org.au/afd/taxa/2cac777c-...                    Pseudophryne pengilleyi  ...        Northern Corroboree Frog                24
21  https://biodiversity.org.au/afd/taxa/2063bf6d-...            Pachycephala (Timixos) olivacea  ...                  Olive Whistler                22
22  https://biodiversity.org.au/afd/taxa/77bcae99-...                     Pteropus poliocephalus  ...          Grey-headed Flying-fox                19
23   https://id.biodiversity.org.au/node/apni/2910323                     Ammobium craspedioides  ...                      Yass Daisy                17
24   https://id.biodiversity.org.au/node/apni/7062437                 Rutidosis leptorhynchoides  ...              Button Wrinklewort                17
25  https://biodiversity.org.au/afd/taxa/6485cd0c-...                      Hirundapus caudacutus  ...       White-throated Needletail                16
26  https://biodiversity.org.au/afd/taxa/a51dca29-...                              Synemon plana  ...                 Golden Sun Moth                14
27  https://biodiversity.org.au/afd/taxa/320aeab1-...             Artamus (Angroyan) cyanopterus  ...               Dusky Woodswallow                11
28  https://biodiversity.org.au/afd/taxa/7dd0c4d5-...                                Delma impar  ...          Striped Legless Lizard                11
29  https://biodiversity.org.au/afd/taxa/003a110b-...                            Keyacris scurra  ...  Keyâs Matchstick Grasshopper                 9
30  https://biodiversity.org.au/afd/taxa/52149285-...                        Dasyurus viverrinus  ...                          Luaner                 8
31  https://biodiversity.org.au/afd/taxa/8f7da937-...                         Bettongia gaimardi  ...               Tasmanian Bettong                 7
32   https://id.biodiversity.org.au/node/apni/2897239                        Grevillea iaspicula  ...            Wee Jasper Grevillea                 7
33  https://biodiversity.org.au/afd/taxa/d1c5dee0-...                Ninox (Rhabdoglaux) strenua  ...                    Powerful Owl                 6
34   https://id.biodiversity.org.au/node/apni/2900093                       Eucalyptus aggregata  ...                       Black Gum                 5
35   https://id.biodiversity.org.au/node/apni/2918079                      Wilsonia rotundifolia  ...             Round-leaf Wilsonia                 5
36  https://id.biodiversity.org.au/taxon/apni/5143...                         Eucalyptus radiata  ...          Narrow-leaf Peppermint                 5
37  https://biodiversity.org.au/afd/taxa/0e912185-...                      Aprasia parapulchella  ...         Pink-tailed Worm-lizard                 2
38  https://biodiversity.org.au/afd/taxa/1b653e0c-...                      Petaurus norfolcensis  ...                 Squirrel Glider                 2
39  https://biodiversity.org.au/afd/taxa/1c950a15-...                    Litoria booroolongensis  ...                 Booroolong Frog                 2
40  https://biodiversity.org.au/afd/taxa/796b9811-...       Petroica (Erythrodryas) rodinogaster  ...                      Pink Robin                 2
41  https://biodiversity.org.au/afd/taxa/d5b6c816-...                Falco (Hierofalco) subniger  ...                    Black Falcon                 2
42   https://id.biodiversity.org.au/node/apni/2897349                         Pomaderris pallida  ...                 Pale Pomaderris                 2
43   https://id.biodiversity.org.au/node/apni/2920809                          Eucalyptus pumila  ...                 Pokolbin Mallee                 2
44  https://id.biodiversity.org.au/taxon/apni/5139...                         Caladenia concolor  ...           Crimson Spider Orchid                 2
45  https://biodiversity.org.au/afd/taxa/1f6b7596-...  Calyptorhynchus (Calyptorhynchus) lathami  ...           Glossy Black-cockatoo                 1
46  https://biodiversity.org.au/afd/taxa/5815e99d-...                     Lophochroa leadbeateri  ...       Major Mitchell's Cockatoo                 1
47  https://biodiversity.org.au/afd/taxa/5f79ff26-...         Climacteris (Climacteris) picumnus  ...               Brown Treecreeper                 1
48  https://biodiversity.org.au/afd/taxa/6231c7a7-...                         Varanus rosenbergi  ...                   Heath Monitor                 1
49  https://biodiversity.org.au/afd/taxa/715a2874-...                         Lophoictinia isura  ...              Square-tailed Kite                 1
50  https://biodiversity.org.au/afd/taxa/8303d47d-...                      Anseranas semipalmata  ...                    Magpie Goose                 1
51  https://biodiversity.org.au/afd/taxa/e9d6fbbd-...                     Phascolarctos cinereus  ...                           Koala                 1
52   https://id.biodiversity.org.au/node/apni/2897969                        Eucalyptus nicholii  ...  Narrow-leaved Black Peppermint                 1
53   https://id.biodiversity.org.au/node/apni/2901250                          Pimelea bracteata  ...                             NaN                 1
54   https://id.biodiversity.org.au/node/apni/2916272                            Swainsona recta  ...                Small Purple-pea                 1
55   https://id.biodiversity.org.au/node/apni/2917559                          Hakea pulvinifera  ...               Lake Keepit Hakea                 1
56  https://id.biodiversity.org.au/taxon/apni/5140...                       Prasophyllum petilum  ...                    A Leekorchid                 1
57  https://id.biodiversity.org.au/taxon/apni/5144...                    Eucalyptus pulverulenta  ...               Silver-leafed Gum                 1
```

We can also return only species in Yass Valley on the New South Wales Conservation Status List (`dr487`) by adding the list ID `species_list_uid=dr487` to filters. Our query returns 12 species.

```python
yass_sensitive = galah.atlas_species(
  polygon=yass_valley["geometry"][127],
  filters=["year=2023", "species_list_uid=dr487"],
  counts=True
  )
yass_sensitive
```

```output
                                              Species                               Species Name  ...            Vernacular Name Number of records
0   https://biodiversity.org.au/afd/taxa/6c646af8-...                   Callocephalon fimbriatum  ...         Gang-gang Cockatoo              1145
1   https://biodiversity.org.au/afd/taxa/46fca72f-...                       Polytelis swainsonii  ...              Superb Parrot               960
2   https://biodiversity.org.au/afd/taxa/a2399fe3-...                Parvipsitta porphyrocephala  ...    Purple-crowned Lorikeet                31
3   https://biodiversity.org.au/afd/taxa/2cac777c-...                    Pseudophryne pengilleyi  ...   Northern Corroboree Frog                24
4    https://id.biodiversity.org.au/node/apni/2897239                        Grevillea iaspicula  ...       Wee Jasper Grevillea                 7
5   https://biodiversity.org.au/afd/taxa/d1c5dee0-...                Ninox (Rhabdoglaux) strenua  ...               Powerful Owl                 6
6   https://id.biodiversity.org.au/taxon/apni/5139...                         Caladenia concolor  ...      Crimson Spider Orchid                 2
7   https://biodiversity.org.au/afd/taxa/1f6b7596-...  Calyptorhynchus (Calyptorhynchus) lathami  ...      Glossy Black-cockatoo                 1
8   https://biodiversity.org.au/afd/taxa/5815e99d-...                     Lophochroa leadbeateri  ...  Major Mitchell's Cockatoo                 1
9   https://biodiversity.org.au/afd/taxa/715a2874-...                         Lophoictinia isura  ...         Square-tailed Kite                 1
10   https://id.biodiversity.org.au/node/apni/2917559                          Hakea pulvinifera  ...          Lake Keepit Hakea                 1
11  https://id.biodiversity.org.au/taxon/apni/5140...                       Prasophyllum petilum  ...               A Leekorchid                 1
```