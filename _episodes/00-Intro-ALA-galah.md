---
title: "What is the ALA and the galah-python package?"
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

The Atlas of Living Australia (ALA) provides everyone, from researchers to citizen scientists, open access to Australia's bioversity data.  This has been used in everything, from scientific research papers on X to information on Y.

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
|                      | `geolocate=`**     |                             |

** COMING SOON

# Ok, now how do I install and use it?  Are there other packages I need to install?

To install `galah-python`, ensure that you have the terminal open.  To do this via Anaconda, go to X and then Y.

In the terminal, type

```bash
pip install galah-python
```

You will need other packages in this workshop, namely `matplotlib` and `geopandas`.  To install them, run

```bash
pip install matplotlib geopandas
```

Then, in the terminal, type

```bash
python
```

and, when you see three `>>>`, type

```python
>>> import galah
>>> import matplotlib
>>> import geopandas
```

If you get no errors, this has installed correctly.

Now that you have installed `galah-python`, I will take you through these two examples: 

- How to get occurrences of the species *Litoria peronii* and plot them on a map
- How to get a list of all the species in the state of Victoria (or the ACT?)

First, I will take you through how to build a query step by step.