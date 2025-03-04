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
0                                 FrogID  39840
1                       NSW BioNet Atlas   4882
2                  iNaturalist Australia   2578
3                             NatureMapr    249
4            Earth Guardians Weekly Feed    151
5      ALA species sightings and OzAtlas     16
6           Victorian Biodiversity Atlas     10
7                           FrogWatch SA      6
8   Australian Museum provider for OZCAM      4
9                              BowerBird      3
10           Melbourne Water Frog Census      2
11                              SA Fauna      2
```

We can see that there are 12 data resources that have provided the ALA observations of *Litoria peronii*, and surprisingly, FrogID provides the second most observations!

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
0                                 FrogID     -  39840
1                       NSW BioNet Atlas     -   4882
2                  iNaturalist Australia     -   2578
3                             NatureMapr     -    249
4            Earth Guardians Weekly Feed     -    151
5      ALA species sightings and OzAtlas     -     16
6           Victorian Biodiversity Atlas     -     10
7                           FrogWatch SA     -      6
8   Australian Museum provider for OZCAM     -      4
9                              BowerBird     -      3
10           Melbourne Water Frog Census     -      2
11                              SA Fauna     -      2
12                                     -  2018   5200
13                                     -  2019   5469
14                                     -  2020  13358
15                                     -  2021  14469
16                                     -  2022   7506
17                                     -  2023    817
18                                     -  2024    762
19                                     -  2025    162
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
0                                 FrogID  2018   4154
1                                 FrogID  2019   4382
2                                 FrogID  2020  12248
3                                 FrogID  2021  12851
4                                 FrogID  2022   6205
5                       NSW BioNet Atlas  2018    850
6                       NSW BioNet Atlas  2019    872
7                       NSW BioNet Atlas  2020    808
8                       NSW BioNet Atlas  2021   1244
9                       NSW BioNet Atlas  2022    840
10                      NSW BioNet Atlas  2023    205
11                      NSW BioNet Atlas  2024     63
12                 iNaturalist Australia  2018    108
13                 iNaturalist Australia  2019    113
14                 iNaturalist Australia  2020    227
15                 iNaturalist Australia  2021    321
16                 iNaturalist Australia  2022    409
17                 iNaturalist Australia  2023    576
18                 iNaturalist Australia  2024    665
19                 iNaturalist Australia  2025    159
20                            NatureMapr  2018     37
21                            NatureMapr  2019     48
22                            NatureMapr  2020     47
23                            NatureMapr  2021     24
24                            NatureMapr  2022     27
25                            NatureMapr  2023     33
26                            NatureMapr  2024     30
27                            NatureMapr  2025      3
28           Earth Guardians Weekly Feed  2018     30
29           Earth Guardians Weekly Feed  2019     43
30           Earth Guardians Weekly Feed  2020     24
31           Earth Guardians Weekly Feed  2021     27
32           Earth Guardians Weekly Feed  2022     22
33           Earth Guardians Weekly Feed  2023      1
34           Earth Guardians Weekly Feed  2024      4
35     ALA species sightings and OzAtlas  2018      7
36     ALA species sightings and OzAtlas  2019      5
37     ALA species sightings and OzAtlas  2020      1
38     ALA species sightings and OzAtlas  2022      3
39          Victorian Biodiversity Atlas  2018      5
40          Victorian Biodiversity Atlas  2019      5
41                          FrogWatch SA  2019      1
42                          FrogWatch SA  2020      3
43                          FrogWatch SA  2023      2
44  Australian Museum provider for OZCAM  2018      4
45                             BowerBird  2018      3
46           Melbourne Water Frog Census  2018      2
47                              SA Fauna  2021      2
```