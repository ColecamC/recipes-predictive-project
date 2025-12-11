# recipes-predictive-project

by Coleman Clougherty (cclougherty@ucsd.edu) and Jamera Mellyn Fernando (jmfernando@ucsd.edu)


---

## Introduction

In this project, our data comes from food.com, which is an online social networking featuring recipes from home cooks and celebrity chefs. The website features more than 500,000 user-generated recipes, to which users can add reviews, modficiations, questions, and photos. 

The data comes from two CSV files: RAW_recipes.csv, which contains recipes and RAW_interactions.csv, which contains reviews and ratings submitted for recipes in RAW_recipes.csv. The merged dataset has 83,782 rows, and includes columns such as:
- 'name' (str): describes the recipe nane 
- 'minutes' (int): minutes to prepare recipe 
- 'tags' (str): Food.com tags for recipes 
- 'nutrition' (str): Nutrition information in the form [calories (#), total fat (PDF), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for "percentage of daily value"

One of the features of food.com is that each recipe has different tags that describe the kind of dishes they are (e.g. 'salsas', 'nut-free', 'french', 'American'). 

Given this, we wanted to investigate the question: **what is the relationship between features, such as cooking time, ingredients, and calories, with different country tags for recipes?**


---

## Cleaning and EDA

Describe, in detail, the data cleaning steps you took and how they affected your analyses. The steps should be explained in reference to the data generating process. Show the head of your cleaned DataFrame (see Part 2: Report for instructions).

**DATA CLEANING**
1. We first merged using a left merge with the two raw datasets and created a column containing the average rating per recipe using a groupby function. We filled all rating of 0 with np.nan because ratings with 0 signifies that the user has not rated the recipe, so the value 0 has no real meaning. 

---

## Assessment of Missingness
**NMAR**
There is no column that explicitly shows NMAR. However, the column with the most missing data, which is 'avg-rating' is Missing By Design. This is because ratings with 0 stars is equivalent to ratings with no ratings at all, hence the user not scoring the recipe yet. Therefore, any recipes with an average rating of 0 just means that the recipe has no reviews yet. 


Here's what a Markdown table looks like. Note that the code for this table was generated _automatically_ from a DataFrame, using

```py
print(counts[['Quarter', 'Count']].head().to_markdown(index=False))
```

| Quarter     |   Count |
|:------------|--------:|
| Fall 2020   |       3 |
| Winter 2021 |       2 |
| Spring 2021 |       6 |
| Summer 2021 |       4 |
| Fall 2021   |      55 |

---

## Hypothesis Testing


---
