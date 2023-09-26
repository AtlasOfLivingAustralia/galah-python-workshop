---
title: "Creating Species lists"
start: true
teaching: 15
exercises: 5
questions:
- "What is a species list?"
- "How do I make a species list"
objectives:
- "Understand what a species list is and its purpose"
- "Generate a list of species from a specified taxonomic rank"
- "Generate a species list with counts of species observations"
keypoints:
- "Species lists are X"
- "You can generate species lists from any specified rank"
- "Generating a species list with counts takes little time and few lines of code"
---

# What is a species list?  Why do they matter?

List of species that belong to a particular taxa, and/or a list of species in a particular area.  

# How to get a species list of all species in the same family as Peron's Tree Frog

#### Get the name of Peron's Tree Frog's family 

First, we need to get the taxonomic ranks of Peron's Tree Frog.  To do this, we can use the function `search_taxa()`, which will list the entire taxonomy of a species.

```python
galah.search_taxa(taxa="Litoria peronii")
```
```output
    scientificName scientificNameAuthorship                                     taxonConceptID     rank   kingdom  ...   family    genus          species     vernacularName   issues
0  Litoria peronii          (Tschudi, 1838)  https://biodiversity.org.au/afd/taxa/c584f24b-...  species  Animalia  ...  Hylidae  Litoria  Litoria peronii  Peron's Tree Frog  noIssue
```

We can see from this output that the family we want to query is *Hylidae*. 

#### Use `atlas_species()` to get a list of all species within the *Hylidae* family

```python
galah.galah_config(email="amanda.buyan@csiro.au")
galah.atlas_species(taxa="Hylidae")
```
```output
                  species                                           author                                       species_guid  ...  order   family             vernacular_name
0         Litoria peronii                                  (tschudi, 1838)  Https://biodiversity.org.au/afd/taxa/c584f24b-...  ...  Anura  Hylidae           Peron's Tree Frog
1          Litoria fallax                                   (peters, 1880)  Https://biodiversity.org.au/afd/taxa/f67a8d4e-...  ...  Anura  Hylidae     Eastern Dwarf Tree Frog
2         Litoria ewingii                         (duméril & bibron, 1841)  Https://biodiversity.org.au/afd/taxa/d0e897bb-...  ...  Anura  Hylidae             Brown Tree Frog
3           Litoria aurea                                   (lesson, 1829)  Https://biodiversity.org.au/afd/taxa/e7adefa4-...  ...  Anura  Hylidae  Green and Golden Bell Frog
4      Litoria verreauxii                                  (duméril, 1853)  Https://biodiversity.org.au/afd/taxa/5557cf29-...  ...  Anura  Hylidae             Verreaux's Frog
..                    ...                                              ...                                                ...  ...    ...      ...                         ...
83  Litoria kroombitensis  Hoskin, hines, meyer, clarke & cunningham, 2013  Https://biodiversity.org.au/afd/taxa/a7a0371e-...  ...  Anura  Hylidae           Kroombit Treefrog
84          Litoria myola                                     Hoskin, 2007  Https://biodiversity.org.au/afd/taxa/533a80d4-...  ...  Anura  Hylidae           Kuranda Tree Frog
85   Litoria andiirrmalin                                   Mcdonald, 1997  Https://biodiversity.org.au/afd/taxa/2c53c748-...  ...  Anura  Hylidae            Andirrmalin Frog
86      Litoria axillaris                                    Doughty, 2011  Https://biodiversity.org.au/afd/taxa/af934861-...  ...  Anura  Hylidae       Kimberley Rocket Frog
87         Litoria lorica                          Davies & mcdonald, 1979  Https://biodiversity.org.au/afd/taxa/2edc1a0c-...  ...  Anura  Hylidae               Armoured Frog
```

# Adding filters: Get a list of all frogs in the family *Hylidae* in Victoria for the year 2022

We already know that we will want to use the string `year=2022`, but we need to double-check what the values for the Australian States field was.  Remembering from Lesson 2 that the field we want is `cl22`, we can use the `show_values()` function again to remind ourselves.

```python
galah.show_values(field="cl22")
```
```output
   field                      category
0   cl22               New South Wales
1   cl22                      Victoria
2   cl22                    Queensland
3   cl22               South Australia
4   cl22             Western Australia
5   cl22            Northern Territory
6   cl22                      Tasmania
7   cl22  Australian Capital Territory
8   cl22                      Unknown1
9   cl22             Coral Sea Islands
10  cl22   Ashmore and Cartier Islands
```

Now that we have confirmed these values, we can add these filters to `atlas_species()`.

```python
galah.atlas_species(taxa="Hylidae",filters=["cl22=Victoria","year=2022"])
```
```output
              species                                   author                                       species_guid   kingdom  ...     class  order   family             vernacular_name
0     Litoria ewingii                 (duméril & bibron, 1841)  Https://biodiversity.org.au/afd/taxa/d0e897bb-...  Animalia  ...  Amphibia  Anura  Hylidae             Brown Tree Frog
1     Litoria peronii                          (tschudi, 1838)  Https://biodiversity.org.au/afd/taxa/c584f24b-...  Animalia  ...  Amphibia  Anura  Hylidae           Peron's Tree Frog
2  Litoria verreauxii                          (duméril, 1853)  Https://biodiversity.org.au/afd/taxa/5557cf29-...  Animalia  ...  Amphibia  Anura  Hylidae             Verreaux's Frog
3  Litoria nudidigita                          (copland, 1962)  Https://biodiversity.org.au/afd/taxa/05612772-...  Animalia  ...  Amphibia  Anura  Hylidae  Leaf Green River Tree Frog
4      Litoria fallax                           (peters, 1880)  Https://biodiversity.org.au/afd/taxa/f67a8d4e-...  Animalia  ...  Amphibia  Anura  Hylidae     Eastern Dwarf Tree Frog
5  Litoria paraewingi  Watson, loftus-hills & littlejohn, 1971  Https://biodiversity.org.au/afd/taxa/58d661f2-...  Animalia  ...  Amphibia  Anura  Hylidae              Victorian Frog
6  Litoria raniformis                       (keferstein, 1867)  Https://biodiversity.org.au/afd/taxa/6e63311a-...  Animalia  ...  Amphibia  Anura  Hylidae            Golden Bell Frog
```

# How to export your species list to csv format

Say you have collaborators who want a species list for the family *Hylidae* for the state of Victoria in the year 2022, but they want a spreadsheet rather than code to generate it.  Using the inbuilt functionality of `pandas`, you can write your data frame directly to a `csv`.

```python
# get list from above into a variable
frogs_list = galah.atlas_species(
    taxa="Hylidae",
    filters=["cl22=Victoria","year=2022"]
)

# Export data to csv
frogs_list.to_csv("hylidae_family_list_vic_2022.csv")
```

# How to export your species list to csv format <u>with counts</u>

Now, say your collaborators want to know how many records of each frog species there were for the family *Hylidae* for the state of Victoria in the year 2022.  By using a combination of `atlas_species()` and `atlas_counts()`, you can generate this table and write it to a `csv`.

```python
galah.galah_config(email="amanda.buyan@csiro.au")
frog_list_vic = galah.atlas_species(
    taxa="Hylidae",
    filters=["cl22=Victoria","year=2022"]
)
species_list_counts = galah.atlas_counts(
    taxa=list(frog_list_vic["species"]),
    filters=["cl22=Victoria","year=2022"],
    group_by="species",
    expand=False
)
print(species_list_counts)
```
```output
              species  count
0     Litoria ewingii    644
1      Litoria fallax     18
2  Litoria nudidigita     24
3  Litoria paraewingi      5
4     Litoria peronii    137
5  Litoria raniformis      3
6  Litoria verreauxii     47
```
```python
species_list_counts.to_csv("hylidae_family_list_vic_2022_counts.csv")
```