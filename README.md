# recipes-predictive-project

by Coleman Clougherty (cclougherty@ucsd.edu) and Jamera Mellyn Fernando (jmfernando@ucsd.edu)


---

## Introduction

In this project, our data comes from food.com, which is an online platform where users can publicly submit personalized recipes to learn from, rate, and review them. The website features user-generated recipes to which users can add reviews, ratings, and photos.

The data comes from two CSV files: RAW_recipes.csv, which contains recipes and RAW_interactions.csv, which contains reviews and ratings submitted for recipes in RAW_recipes.csv. The merged dataset has 234,429 rows and includes columns such as:
- 'name' (str): describes the recipe name
- 'minutes' (int): minutes to prepare recipe
- 'tags' (str): Food.com tags for recipes
- 'rating' (float): individual user ratings for recipes
- 'avg_rating' (float): average rating per recipe
- 'nutrition' (str): Nutrition information including calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates

One of the features of food.com is that each recipe has different tags that describe the kind of dishes they are (e.g. 'salsas', 'nut-free', 'french', 'mexican'). 

Given this, we wanted to investigate the question: **what is the relationship between features, such as cooking time, ingredients, and calories, with country of origin for different recipes?**


---

## Cleaning and EDA

Describe, in detail, the data cleaning steps you took and how they affected your analyses. The steps should be explained in reference to the data generating process. Show the head of your cleaned DataFrame (see Part 2: Report for instructions).

**DATA CLEANING**

1. **Merging datasets**: We first merged the two raw datasets (RAW_recipes.csv and RAW_interactions.csv) using a left merge on recipe ID. This allowed us to connect recipe information with user ratings and reviews.

2. **Handling missing ratings**: We replaced all ratings of 0 with `np.nan` because ratings with 0 signify that the user has not rated the recipe, so the value 0 has no real meaning. We then calculated the average rating per recipe using a groupby function.

3. **Processing tags**: The tags column was stored as a string representation of a list, so we canonicalized it by converting each tag entry into an actual list of individually quoted tags. We created a new column 'tags_list' to store these processed tags. This transformation is important for understanding how tags describe recipes and analyzing relationships between specific tags and recipe features.

4. **Extracting nutrition information**: We parsed the nutrition column to extract individual nutritional values (calories, total fat, sugar, sodium, protein, saturated fat, carbohydrates) into separate columns for easier analysis.

5. **Creating continent feature**: We created a 'continent_of_origin' column by identifying country-specific tags (e.g., 'mexican', 'french', 'australian') and mapping them to their respective continents.

**Example of cleaned tags_list:**
```
['30-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'cuisine', 
 'preparation', 'north-american', 'main-dish', 'beans', 'vegetables', 'mexican', 
 'easy', 'beginner-cook', 'kid-friendly', 'vegetarian', 'dietary', 
 'one-dish-meal', 'inexpensive']
```

**Head of cleaned DataFrame (subset of key columns):**

| id | continent | minutes | n_steps | n_ingredients | avg_rating | calories |
|----|-----------|---------|---------|---------------|------------|----------|
| 453467 | NA | 40 | 12 | 9 | 5.0 | 595.1 |
| 286009 | NA | 120 | 7 | 7 | 5.0 | 878.3 |
| 333797 | NA | 85 | 11 | 12 | 5.0 | 267.8 |

Additional nutritional columns (fat, sugar, sodium, protein, etc.) were omitted for readability.



### Distribution of Average Ratings in Recipes

![Distribution of Average Ratings](visualizations/average_recipe_ratings.png)

As seen in the graph above, the distribution is unimodal with a strong skew to the left. Higher rated recipes (4-5 stars) have the highest count of reviews, with recipes rated 5 stars having over 50,000 reviews. This could be because popular recipes, made by popular chefs or well-tested recipes, are more critically acclaimed and praised. In contrast, recipes with ratings below 3 stars have significantly fewer reviews (under 10,000), suggesting that poorly rated recipes may receive less attention or engagement from users.

![Number of Ratings per Recipe](visualizations/ratings_per_recipe.png)
Conclusion: The median number of recipes have a relatively lower number of ratings, however, there is a large variance to the dataset, with many recipes being an extreme outlier to the dataset. This shows the large gap between popular and unpopular recipes.


**FEATURE ENGINEERING**

Since we wanted to investigate ingredients to see if there is a relationship between ingredients and continent, we transformed the ingredients column (which was stored as a string) into a proper list format for easier analysis.

We identified recipes by continent of origin using the following continent-specific tags:
```python
continent_tags = [
    'north-american',
    'south-american',
    'european',
    'asian',
    'african',
    'australian'
]
```

**Distribution of Recipes by Continent:**

| Continent        | Count  |
|------------------|--------|
| north-american   | 14,590 |
| european         | 8,897  |
| asian            | 4,575  |
| african          | 1,357  |
| australian       | 1,151  |
| south-american   | 689    |

We created a new dataframe with recipes that have continent tags, which contains the following columns:
- `id`, `continent_of_origin`, `value`, `name`, `minutes`, `contributor_id`, `submitted`, `n_steps`, `steps`, `description`, `ingredients`, `n_ingredients`, `avg_rating`, `tags_list`, `calories`, `total_fat`, `sugar`, `sodium`, `protein`, `saturated_fat`, `carbohydrates`

**Sample of continent-tagged recipes:**

| id      | continent_of_origin | value | name                              | sodium | protein | saturated_fat | carbohydrates |
|---------|---------------------|-------|-----------------------------------|--------|---------|---------------|---------------|
| 453467  | north-american      | True  | 1 in canada chocolate chip cookies| 22.0   | 13.0    | 51.0          | 26.0          |
| 286009  | north-american      | True  | millionaire pound cake            | 13.0   | 20.0    | 123.0         | 39.0          |
| 333797  | north-american      | True  | after med flsk pea soup with pork | 34.0   | 44.0    | 12.0          | 0.0           |
| ...     | ...                 | ...   | ...                               | ...    | ...     | ...           | ...           |
| 359208  | australian          | True  | zucchini milano                   | 1.0    | 2.0     | 0.0           | 1.0           |
| 349024  | australian          | True  | zucchini mustard chicken burgers  | 6.0    | 53.0    | 5.0           | 12.0          |
| 441846  | australian          | True  | zucchini feta and dill pie        | 47.0   | 50.0    | 69.0          | 7.0           |

The continent-tagged dataset represents 37.82% of the total recipes dataset, indicating that a significant portion of recipes have identifiable continent-of-origin tags.

**UNIVARIATE ANALYSIS**

### Distribution of Recipes by Continent of Origin

![Distribution by Continent](visualizations/continent_distribution.png)

The distribution shows that North American recipes dominate the dataset with 14,590 recipes (47% of continent-tagged recipes), followed by European recipes at 8,897 (29%). Asian recipes account for 4,575 (15%), while African, Australian, and South American recipes are less represented with 1,357, 1,151, and 689 recipes respectively.

This imbalance may become an issue if we perform classification tasks, as the model will likely be biased towards North American dishes due to the significantly larger amount of training data available for this category.

### Distribution of North American Recipes by Percent Calories from fat

![Percent_calories_north_american](visualizations/pct_cal_fat_Namerica.png)

Since North America has the highest recipe count, we decided to focus our initial comparison on this continent. 

We engineered a new column, `percent_calories_from_fat`, which standardizes the amount of fat relative to total calories in recipes. This allows for a fairer comparison across recipes with different caloric values.  This distribution shows a fairly symmetrical distribution, with the center around 45-55%. 


**BIVARIATE ANALYSIS**

### Comparison of Calories: North American vs. Non-North American Recipes

| is_north_american | Average Calories |
|-------------------|------------------|
| False             | 436.64           |
| True              | 448.03           |

We wanted to compare the distributions of calories and fat content in North American recipes versus Non-North American recipes to investigate if there are any differences in nutritional profiles between continents.

### Boxplots of different features by Continent 

Going back to the hypothesis question, we wanted to explore the relationships between different features to continents of different recipes. While North America is the main continent we used to test, along with calories as the feature, we wanted to explore how other continents compare to each other. 

Here are some figures that show the boxplots of each continent using n_ingredients and avg_rating. 

![Boxplot of n_ingredients by continent](visualizations/n_ing_by_continent.svg)

![Boxplot of avg_rating by continent](visualizations/avg_rating_by_continent.svg)

### Distribution of Percent Calories from Fat: North American vs. Non-North American

![Calories from Fat Distribution](visualizations/overlay_distribution_NA.png)

The distributions of percent calories from fat appear similar between North American and Non-North American recipes. Both distributions show a roughly normal shape with a slight right skew, centered around 40-50% of calories from fat. This suggests that, at least in terms of fat content relative to calories, there is no substantial difference in the "healthiness" profile between North American recipes and recipes from other continents in this dataset.

**INTERESTING AGGREGATES**

### Average Nutritional Values by Continent

| continent_of_origin | avg_calories | avg_total_fat | avg_sugar | avg_sodium | avg_protein |
|---------------------|--------------|---------------|-----------|------------|-------------|
| african             | 385.2        | 34.5          | 67.8      | 28.4       | 35.2        |
| asian               | 392.1        | 31.2          | 45.3      | 35.6       | 38.7        |
| australian          | 421.8        | 38.9          | 78.2      | 24.1       | 42.3        |
| european            | 445.7        | 39.8          | 82.1      | 26.8       | 36.9        |
| north-american      | 448.0        | 40.2          | 85.4      | 27.3       | 37.1        |
| south-american      | 412.3        | 35.7          | 71.2      | 31.2       | 36.8        |

This aggregate table reveals some interesting patterns. North American and European recipes tend to have higher average calories and sugar content compared to Asian and African recipes. However, Asian recipes show the highest average sodium content. African recipes have the lowest average calorie count while still maintaining a reasonable protein level.

---

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
