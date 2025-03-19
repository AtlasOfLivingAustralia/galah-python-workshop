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
1                       NSW BioNet Atlas   4884
2                  iNaturalist Australia   2664
3            Earth Guardians Weekly Feed    150
4                             NatureMapr    133
5      ALA species sightings and OzAtlas     16
6           Victorian Biodiversity Atlas     10
7                           FrogWatch SA      6
8   Australian Museum provider for OZCAM      4
9                              BowerBird      3
10           Melbourne Water Frog Census      2
11                              SA Fauna      2
```

We can see that there are 12 data resources that have provided the ALA observations of *Litoria peronii*.

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
1                       NSW BioNet Atlas     -   4884
2                  iNaturalist Australia     -   2664
3            Earth Guardians Weekly Feed     -    150
4                             NatureMapr     -    133
5      ALA species sightings and OzAtlas     -     16
6           Victorian Biodiversity Atlas     -     10
7                           FrogWatch SA     -      6
8   Australian Museum provider for OZCAM     -      4
9                              BowerBird     -      3
10           Melbourne Water Frog Census     -      2
11                              SA Fauna     -      2
12                                     -  2018   5181
13                                     -  2019   5447
14                                     -  2020  13334
15                                     -  2021  14458
16                                     -  2022   7496
17                                     -  2023    800
18                                     -  2024    753
19                                     -  2025    245
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
11                      NSW BioNet Atlas  2024     65
12                 iNaturalist Australia  2018    108
13                 iNaturalist Australia  2019    113
14                 iNaturalist Australia  2020    228
15                 iNaturalist Australia  2021    321
16                 iNaturalist Australia  2022    410
17                 iNaturalist Australia  2023    577
18                 iNaturalist Australia  2024    666
19                 iNaturalist Australia  2025    241
20           Earth Guardians Weekly Feed  2018     30
21           Earth Guardians Weekly Feed  2019     43
22           Earth Guardians Weekly Feed  2020     22
23           Earth Guardians Weekly Feed  2021     26
24           Earth Guardians Weekly Feed  2022     22
25           Earth Guardians Weekly Feed  2023      1
26           Earth Guardians Weekly Feed  2024      6
27                            NatureMapr  2018     18
28                            NatureMapr  2019     26
29                            NatureMapr  2020     24
30                            NatureMapr  2021     14
31                            NatureMapr  2022     16
32                            NatureMapr  2023     15
33                            NatureMapr  2024     16
34                            NatureMapr  2025      4
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