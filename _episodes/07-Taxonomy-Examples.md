---
title: "How to disambiguate taxonomy"
start: true
teaching: 20
exercises: 15
questions:
- "How do I deal with homonyms in the ALA?"
- "How do I look up more detailed taxonomic information?"
- "How do I use this information to look up counts and occurrences?"
objectives:
- "Disambiguate a species using the `scientific_name` argument"
- "Identify filters to use for your data, their possible values, and add it to your galah query"
- "Begin to learn when you use `scientific_name` vs. `filter=`."
keypoints:
- "When looking up taxa, getting the right scientific name may not be straightforward."
- "Filtering your data on taxonomic names can help with disambiguation."
- "Providing extra arguments can help get you the data you want."
---

#### *Note: based on* https://galah.ala.org.au/Python/galah_user_guide/Taxonomic_Filtering.html#taxonomic-filtering

Taxonomic complexity can confound the process of searching, filtering, and downloading records using galah, but there are a few ways to ensure records are not missed by using [functions] in galah. Let’s start by configuring galah to the ALA.

```python
import galah
galah.galah_config(atlas="Australia",email="your-email-here")
```
# Learning about `search_taxa()`

`search_taxa()` enables users to look up taxonomic names before downloading data, which allows for disambiguating homonyms and checking that the search term matches the taxon name in the ALA . `search_taxa()` returns the scientific name, authorship, rank, and full classification for the taxon matched to the provided search term.

```python
galah.search_taxa(taxa="Petroica boodang")
```
```output
                scientificName scientificNameAuthorship                                     taxonConceptID  ...           species vernacularName   issues
0  Petroica (Petroica) boodang           (Lesson, 1838)  https://biodiversity.org.au/afd/taxa/a3e5376b-...  ...  Petroica boodang  Scarlet Robin  noIssue
```

It can also return taxonomic information for multiple species, including synonyms and Indigneous names.

```python
# Muscicapa chrysoptera is a synonym for the Flame Robin, Petroica phoenicea
# Guniibuu is the Yuwaalaraay Indigenous name for the Red-Capped Robin, Petroica goodenovii
galah.search_taxa(taxa = ["Muscicapa chrysoptera", "Guniibuu"])
```
```output
                   scientificName    scientificNameAuthorship  ...    vernacularName   issues
0   Petroica (Littlera) phoenicea                 Gould, 1837  ...       Flame Robin  noIssue
1  Petroica (Petroica) goodenovii  (Vigors & Horsfield, 1827)  ...  Red-capped Robin  noIssue
```

Where homonyms exist, `search_taxa()` will prompt users to clarify the search term by providing one or more taxonomic ranks using the `search_taxa()` argument `scientific_name`. This example differentiates among the genus *Morganella* in three kingdoms:

```python
galah.search_taxa(taxa = ["Morganella"])
```
```output
Warning: Search returned multiple taxa due to a homonym issue.
Please use the `scientific_name` argument to clarify taxa.
  search_term   issues
0  Morganella  homonym
```

```python
galah.search_taxa(scientific_name={"kingdom": ["Fungi"],"scientificName": ["Morganella"]})
```
```output
  scientificName scientificNameAuthorship                                     taxonConceptID   rank  ...       order       family       genus   issues
0     Morganella                   Zeller  https://id.biodiversity.org.au/node/fungi/6009...  genus  ...  Agaricales  Agaricaceae  Morganella  noIssue
```

This disambiguation of the *Morganella* taxa can then be used by `atlas_counts()`, `atlas_occurrences()`, `atlas_species()` or `atlas_media()` by providing the keyword `scientific_name` to any of these functions.

### `atlas_counts()`
```python
galah.atlas_counts(scientific_name={"kingdom": ["Fungi"],"scientificName": ["Morganella"]})
```
```output
   totalRecords
0           149
```

### `atlas_occurrences()`
```python
galah.atlas_occurrences(scientific_name={"kingdom": ["Fungi"],"scientificName": ["Morganella"]})
```
```output
     decimalLatitude  decimalLongitude  ...                                   dataResourceName occurrenceStatus
0         -47.000000        168.200000  ...    New Zealand Fungal and Plant Disease Collection          PRESENT
1         -46.879900        168.136500  ...    New Zealand Fungal and Plant Disease Collection          PRESENT
2         -46.874875        168.124660  ...    New Zealand Fungal and Plant Disease Collection          PRESENT
3         -46.862757        168.116777  ...    New Zealand Fungal and Plant Disease Collection          PRESENT
4         -46.554617        169.479051  ...    New Zealand Fungal and Plant Disease Collection          PRESENT
..               ...               ...  ...                                                ...              ...
144              NaN               NaN  ...   Royal Botanic Gardens, Kew - Fungarium Specimens          PRESENT
145              NaN               NaN  ...   Royal Botanic Gardens, Kew - Fungarium Specimens          PRESENT
146       -22.500000        145.000000  ...     USDA United States National Fungus Collections          PRESENT
147              NaN               NaN  ...     USDA United States National Fungus Collections          PRESENT
148        -8.916667        148.150000  ...  Centre for Australian National Biodiversity Re...          PRESENT

[149 rows x 8 columns]
```

# OPTIONAL: Using filters= to search for exact matches
`filters=` subsets records by searching for exact matches to an expression, and may also be used for taxonomic filtering. for example, if we want to search for multiple species of robins in Australia, we can do this for single or multiple species. We can also group the multiple species by their species names so we can compare the number of records for each robin.

```python
galah.atlas_counts(taxa="Petroica boodang")
```
```output
   totalRecords
0        133664
```

```python
aus_petroica = ["Petroica boodang", "Petroica goodenovii",
                "Petroica phoenicea", "Petroica rosea",
                "Petroica rodinogaster", "Petroica multicolor"]
galah.atlas_counts(taxa=aus_petroica,group_by=["species","vernacularName"])
```
```output
                  species               vernacularName   count
0        Petroica boodang        Eastern Scarlet Robin    3766
1        Petroica boodang                Scarlet Robin  129596
2        Petroica boodang  South-western Scarlet Robin     211
3        Petroica boodang      Tasmanian Scarlet Robin      91
4     Petroica goodenovii             Red-capped Robin  120523
5     Petroica multicolor                Pacific Robin    6795
6      Petroica phoenicea                  Flame Robin   88884
7   Petroica rodinogaster          Mainland Pink Robin      69
8   Petroica rodinogaster                   Pink Robin   15753
9   Petroica rodinogaster         Tasmanian Pink Robin      45
10         Petroica rosea                   Rose Robin   60276
```

This can be useful in searching for [paraphyletic](https://en.wikipedia.org/wiki/Paraphyly) or [polyphyletic](http://en.wikipedia.org/wiki/Polyphyly) groups. For example, to get counts of non-chordates:

```python
non_chordates = galah.atlas_counts(
    filters=["kingdom=Animalia","phylum!=Chordata"],
    group_by=["phylum"],
    expand=False
)
non_chordates.head()
```
```output
           phylum     count
0  Acanthocephala       481
1        Annelida    329585
2      Arthropoda  10086467
3     Brachiopoda     11574
4         Bryozoa     32837
```

# OPTIONAL: Deciding between `filters=`, `search_taxa()`, and taxonomic ranks
Deciding between using `filters=` and `search_taxa()` in a query comes down to how a record has been classified, and whether or not you have the correct unique name and classification of the taxa of interest.

The ALA has fields for the primary taxonomic ranks (kingdom, phylum, class, order, family, genus, species) and some secondary ranks (e.g. subfamily, subgenus), all of which may be used with filters= and search_taxa(). Additionally, there is a field named scientificName, which refers to the lowest taxonomic rank to which a record has been identified e.g.

```python
import numpy as np
pitta_ranks = galah.atlas_counts(
    taxa="Pitta",
    group_by=["scientificName","taxonRank"]
)
pitta_ranks = pitta_ranks.loc[pitta_ranks["scientificName"].notnull()]
pitta_ranks
```
```output
                                 scientificName   taxonRank  count
0                                         Pitta       genus     70
1                          Pitta (Erythropitta)    subgenus    882
2            Pitta (Erythropitta) erythrogaster     species    189
3   Pitta (Erythropitta) erythrogaster digglesi  subspecies      6
4                            Pitta (Pitta) iris     species   6599
5                       Pitta (Pitta) iris iris  subspecies     91
6              Pitta (Pitta) iris johnstoneiana  subspecies     27
7                      Pitta (Pitta) versicolor     species  30240
8           Pitta (Pitta) versicolor intermedia  subspecies     64
9            Pitta (Pitta) versicolor simillima  subspecies     53
10          Pitta (Pitta) versicolor versicolor  subspecies    406
```

If, for instance, you have the correct species or subspecies name, then searching for matches against the species and subspecies fields, respectively, will provide more precise results. This is because the field `scientificName` may include subgenera. If you’ve used `search_taxa()` to get the ALA-matched name of a taxon and only want records identified to a particular level of classification, searching for matches against `scientificName` is recommended.

[Paraphyletic](https://en.wikipedia.org/wiki/Paraphyly) or [polyphyletic](http://en.wikipedia.org/wiki/Polyphyly) groups may contain taxa identified to different taxonomic levels. In this case, it is simpler to use `search_taxa()`. In the example below, `search_taxa()` matches terms to one genus, three species, and two subspecies. This can then be used in `atlas_counts()` to get counts for each scientific name.

```python
tas_endemic = ["Sarcophilus", # Tasmanian Devil
               "Bettongia gaimardi", # Tasmanian Bettong
               "Melanodryas vittata", # Dusky Robin
               "Platycercus caledonicus", # Green Rosella
               "Aquila audax fleayi", # Tasmanian Wedge-Tailed Eagle
               "Tyto novaehollandiae castanops" # Tasmanian Masked Owl
              ]
galah.search_taxa(taxa=tas_endemic)
```
```output
                          scientificName scientificNameAuthorship  ...                  species                vernacularName
0                            Sarcophilus             Cuvier, 1837  ...                      NaN                           NaN
1                     Bettongia gaimardi        (Desmarest, 1822)  ...       Bettongia gaimardi             Tasmanian Bettong
2      Melanodryas (Amaurodryas) vittata   (Quoy & Gaimard, 1830)  ...      Melanodryas vittata                   Dusky Robin
3  Platycercus (Platycercus) caledonicus           (Gmelin, 1788)  ...  Platycercus caledonicus                 Green Rosella
4         Aquila (Uroaetus) audax fleayi    Condon & Amadon, 1954  ...             Aquila audax  Tasmanian Wedge-tailed Eagle
5         Tyto novaehollandiae castanops            (Gould, 1837)  ...     Tyto novaehollandiae          Tasmanian Masked Owl
```

```python
galah.atlas_counts(
    taxa=tas_endemic,
    group_by=["scientificName"],
    expand=False
)
```
```output
                                       scientificName  count
0                      Aquila (Uroaetus) audax fleayi   5088
1                                  Bettongia gaimardi   2282
2                        Bettongia gaimardi cuniculus     54
3                         Bettongia gaimardi gaimardi      9
4                   Melanodryas (Amaurodryas) vittata  15799
5             Melanodryas (Amaurodryas) vittata kingi     16
6           Melanodryas (Amaurodryas) vittata vittata     60
7               Platycercus (Platycercus) caledonicus  51480
8       Platycercus (Platycercus) caledonicus brownii     24
9   Platycercus (Platycercus) caledonicus caledonicus     50
10                                        Sarcophilus    130
11                               Sarcophilus harrisii  36554
12                     Tyto novaehollandiae castanops     85
```