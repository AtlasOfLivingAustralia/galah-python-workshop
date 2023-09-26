---
title: "Grouping counts to gain a deeper understanding of the data"
start: true
teaching: 10
exercises: 10
questions:
- "What does \"grouping counts\" mean?"
- "How can I use it to give me a better understanding of the data"
objectives:
- "Understand what \"grouping counts\" means"
- "Learn how to group ALA data and interpret it"
keypoints:
- "Grouping data can provide valuable insights into what kind of data is avilable on the ALA"
- "This grouping can also serve to better filer your queries"
---

# Group counts by fields

When looking into data such as species occurrences, there may be angles that are hidden by the raw counts of records in the ALA.  For example, we could see in our previous query that the number of records for *Litoria peronii* since 2018 in NSW dropped from 61952 to 27969 when we specified we only want records that were documented by FrogID.  But what other data resources are we leaving out, and how many records are they each responsible for?

To do this, we will use the `group_by` option in `atlas_counts()`.  Any of the fields specified for `filters` can be used in `group_by`.  To group your counts, add `group_by="dataResourceName"` to your query, as well as `expand=False` (the `expand` argument will be explained in detail below):

```python
galah.atlas_counts(
    taxa="litoria peronii",
    filters=["year>=2018",
             "cl22=New South Wales"],
    group_by="dataResourceName",
    expand=False
)
```
```output
                                     dataResourceName  count
0                                    NSW BioNet Atlas  31671
1                                              FrogID  27969
2                               iNaturalist Australia   1432
3                                        FrogWatch SA    582
4                         Earth Guardians Weekly Feed    157
5                                          NatureMapr     96
6                   ALA species sightings and OzAtlas     16
7                                            SA Fauna     16
8                Australian Museum provider for OZCAM      4
9                                           BowerBird      3
10                        Melbourne Water Frog Census      3
11  South Australian Museum Adelaide provider for ...      2
12                       Victorian Biodiversity Atlas      1
```

We can see that there are 13 data resources that have provided the ALA observations of *Litoria peronii*, and surprisingly, FrogID provides the second most observations!

Now, in the query above, we specified that we want records since 2018.  However, we can also see how many records came from each year by adding `year` to the `group_by` arguments.

```python
galah.atlas_counts(
    taxa="litoria peronii",
    filters=["year>=2018",
             "cl22=New South Wales"],
    group_by=["dataResourceName","year"],
    expand=False
)
```
```output
                                     dataResourceName  year  count
0                                    NSW BioNet Atlas     -  31671
1                                              FrogID     -  27969
2                               iNaturalist Australia     -   1432
3                                        FrogWatch SA     -    582
4                         Earth Guardians Weekly Feed     -    157
5                                          NatureMapr     -     96
6                   ALA species sightings and OzAtlas     -     16
7                                            SA Fauna     -     16
8                Australian Museum provider for OZCAM     -      4
9                                           BowerBird     -      3
10                        Melbourne Water Frog Census     -      3
11  South Australian Museum Adelaide provider for ...     -      2
12                       Victorian Biodiversity Atlas     -      1
13                                                  -  2018   9613
14                                                  -  2019   9977
15                                                  -  2020  26190
16                                                  -  2021  14574
17                                                  -  2022   1235
18                                                  -  2023    363
```

Now, we not only have the data resources providing observations of *Litoria peronii*, we can also see how many observations there were per year.

But what if you wanted to know, for each year, how many records each data resource provided?  

This is where the `expand=True` option comes in.  This option will tell `galah-python` that you want to see the number of observations for each dadta resource in each year specified.

#### Note: `expand=True` option is the default, and is only possible when you have more than one option for `group_by`; otherwise, you will get an error.

```python
galah.atlas_counts(
    taxa="litoria peronii",
    filters=["year>=2018",
             "cl22=New South Wales"],
    group_by=["dataResourceName","year"],
)
```
```output
                                     dataResourceName  year  count
0                                    NSW BioNet Atlas  2018   5032
1                                              FrogID  2018   4329
2                               iNaturalist Australia  2018    101
3                                        FrogWatch SA  2018     82
4                         Earth Guardians Weekly Feed  2018     35
5                                          NatureMapr  2018     17
6                   ALA species sightings and OzAtlas  2018      7
7                Australian Museum provider for OZCAM  2018      4
8                                           BowerBird  2018      3
9                         Melbourne Water Frog Census  2018      3
10                                   NSW BioNet Atlas  2019   5209
11                                             FrogID  2019   4509
12                              iNaturalist Australia  2019    111
13                                       FrogWatch SA  2019     71
14                        Earth Guardians Weekly Feed  2019     45
15                                         NatureMapr  2019     26
16                  ALA species sightings and OzAtlas  2019      5
17                                           SA Fauna  2019      1
18                                   NSW BioNet Atlas  2020  13229
19                                             FrogID  2020  12551
20                              iNaturalist Australia  2020    224
21                                       FrogWatch SA  2020    136
22                        Earth Guardians Weekly Feed  2020     25
23                                         NatureMapr  2020     23
24                  ALA species sightings and OzAtlas  2020      1
25                       Victorian Biodiversity Atlas  2020      1
26                                   NSW BioNet Atlas  2021   7492
27                                             FrogID  2021   6580
28                              iNaturalist Australia  2021    306
29                                       FrogWatch SA  2021    138
30                        Earth Guardians Weekly Feed  2021     28
31                                           SA Fauna  2021     15
32                                         NatureMapr  2021     13
33  South Australian Museum Adelaide provider for ...  2021      2
34                                   NSW BioNet Atlas  2022    635
35                              iNaturalist Australia  2022    408
36                                       FrogWatch SA  2022    151
37                        Earth Guardians Weekly Feed  2022     24
38                                         NatureMapr  2022     14
39                  ALA species sightings and OzAtlas  2022      3
40                              iNaturalist Australia  2023    282
41                                   NSW BioNet Atlas  2023     74
42                                       FrogWatch SA  2023      4
43                                         NatureMapr  2023      3
```