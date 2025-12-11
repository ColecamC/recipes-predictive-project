# recipes-predictive-project

by Coleman Clougherty (cclougherty@ucsd.edu) and Jamera Mellyn Fernando (jmfernando@ucsd.edu)


---

## Introduction

In this project, our data comes from food.com, which is an online social networking featuring recipes from home cooks and celebrity chefs. The website features more than 500,000 user-generated recipes, to which users can add reviews, modficiations, questions, and photos. 

This data has 83,782 rows, and includes columns such as:
- 'name' (str): describes the recipe nane 
- 'minutes' (int): minutes to prepare recipe 
- 'tags' (str): Food.com tags for recipes 
- 'nutrition' (str): Nutrition information in the form [calories (#), total fat (PDF), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for "percentage of daily value"

One of the features of food.com is that each recipe has different tags that describe the kind of dishes they are (e.g. 'salsas', 'nut-free', 'french', 'American'). 

Given this, we wanted to investigate the question: **what is the relationship between features, such as cooking time, ingredients, and calories, with different country tags for recipes?**


---

## Cleaning and EDA

<iframe src="assets/10-80-enrollment.html" width=800 height=600 frameBorder=0></iframe>

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