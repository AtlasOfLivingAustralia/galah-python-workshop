---
title: "History of the `galah-python` package"
start: true
teaching: 10
exercises: 0
questions:
- "What is the ALA?"
- "What is the history of the galah package?"
- "Why is there an extension to Python?"
objectives:
- "Understand what the ALA is and its use."
- "Know the history of the galah software package"
- "Know why it has been extended to the Python programming language"
keypoints:
- "Data providers from all over Australia share data with the ALA, including citizen scientists, governments, museums and other collections."
- "Getting data from the ALA via a programming language has been facilitated with the galah package"
- "Galah has been extended to Python to increase the user base of the ALA data"
---

# What is the ALA?

The Atlas of Living Australia (ALA) provides everyone, from researchers to citizen scientists, open access to Australia's bioversity data.  This has been used in everything, from scientific research papers on [photographs as an essential biodiversity resource](https://nph.onlinelibrary.wiley.com/doi/full/10.1111/nph.18813) to information on [how citizen science aids prediction of habitat suitability](https://escholarship.org/uc/item/98q8s3bv).

# What is the history of the galah package?

`galah` was not always named `galah`.  In fact, it started out as a package titled `ala4r`, which was the first attempt at writing and releasing a package that directly downloaded ALA data into R.  However, it had some problems:

- No function naming convention
- Confusing syntax
- Inconsistent behaviour

These took the form of: 

- abbreviations: `aus()`
- snake case: `ala_fields()`
- single words: `occurrences(), images()`
- contractions: `fieldguide()`
- unclear function names: `ala_list(), ala_lists(), specieslist()`
- abbreviated argument names: `wkt, fq, qa`
- required `solr` queries: `"taxon_name:\"Alaba vibex\""`

These function then returned one of three things:

- `data.frame` (equivalent to `pandas` dataframe in Python)
- `list`
- `PDF`

Using the tidyverse, the `ala4r` package was then renamed and rearchitectured into `galah`, making it more user friendly and able to query the ALA, as well as other national GBIF atlases.  This is reflected by the following functions:

| Lookup         | Narrow a query      | Run a query           |
|----------------|---------------------|-----------------------|
| `show_all()`   | `galah_identify()`  | `atlas_counts()`      |
| `search_all()` | `galah_filter()`    | `atlas_occurrences()` |
|                | `galah_select()`    | `atlas_species()`     |
|                | `galah_group_by()`  | `atlas_media()`       |
|                | `galah_geolocate()` |                       |

# Why is there an extension to Python?

The Python programming langauge is one of the most widely used programming languages in the world.  According to [the Institude of Electrical and Electronics Engineers (IEEE)](https://spectrum.ieee.org/the-top-programming-languages-2023) and [PopularitY of Programming Language (PYPL)](https://pypl.github.io/PYPL.html), Python consistently ranks #1 in popularity and demand for employers, and is a great general-purpose language.  It shines when it is put to use in data analysis, data visualisation, and machine learning.

To ensure that the R and Python packages are as similar as possible, we structured the Python package to still be Pythonic, but behave in a similar fashion to the R program.  This is reflected in the list:


| Lookup               | Narrow a query     | Run a query                 |
|----------------------|--------------------|-----------------------------|
| `galah.show_all()`   | `taxa=`            | `galah.atlas_counts()`      |
| `galah.search_all()` | `filters=`         | `galah.atlas_occurrences()` |
|                      | `select=`          | `galah.atlas_species()`     |
|                      | `group_by=`        | `galah.atlas_media()`       |
|                      | `polygon=`         |                             |
|                      | `bbox=`            |                             |