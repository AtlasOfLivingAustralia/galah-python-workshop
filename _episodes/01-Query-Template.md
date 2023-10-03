---
title: "Guide and Template to Query Building in galah-python"
start: true
teaching: 10
exercises: 0
questions:
- "How do I build a query using galah-python?"
objectives:
- "Understand the template for how to build query in galah-python"
- "Use this template to build more and more complex queries"
keypoints:
- "Builing queries in galah-python follow similar rules and building blocks"
- "Start with simple queries, then build your way up to more complex queries"
---

# What are the building blocks of a query in galah-python?

A query is a command, or line of code, used to get data from an atlas.  We will be working in the Atlas of Living Australia, or the ALA.  

To build a query, follow these steps:

1. Choose whether or not you are looking for a species
    - Look up species name
    - Ensure you have correct species 
2. Choose how to filter the data
    - Look up all possible filters
    - Look up filters by a word or part of a word
3. Choose the values for the filters
    - Look up all possible values
    - If there are lots of values, search for values using a number or part of a word
4. Get counts, and eventually, occurrences

# What does a query look like?

A query can be as simple as the following:

```python
>>> galah.atlas_counts()
```
```output
    totalRecords
0      130570372
```

This returns the total number of records in the Atlas of Living Australia.  This will change weekly, so this number will likely be higher for you!

# Example query

Now, we will build the following query step by step in the next lesson: 

#### Download record counts and occurrence records of Peron's tree frog since 2018 in New South Wales by FrogID