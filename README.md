# Recipes
A project for DSC 80 at UCSD. Some good advice to pick a good recipe💓

---
## Introduction
**Analysis Question:** Is there a relationship between the preparing time and average rating of recipes?
**Why is this important:** By analyzing this dataset, we will identify whether the preparing time and average rating of recipes are related. If there is a relationship, we can help readers filter out the recipes that are more likely to be bad by looking at its preperation time.


### Dataset Information
We have 83782 **rows** in the dataset, and the relevant columns are 
- `'name'` : Recipe name
- `'id'`: Recipe ID
- `'minutes'` : Minutes to prepare recipe as integers
- `'nutrition'` : Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value”
- `'description'` : User-provided description
- `'n_ingredients'` : number of ingredients in the recipe
- `'rating'` : Average Rating of the recipe as floats

---

## Cleaning and EDA
### Data Cleaning
#### Data Cleaning Steps: 
1.  In the getting the data part, we have two dataframes, `'recipes'` and `'interactions'`. Left merge them.
2.  In the merged dataset, fill all ratings of 0 with np.nan. This is **neccessary** because if a recipe is rated 0, it means a reviewer did not enter the rating. So it can be seen as a column containing missing values.
3.  Find the average rating of invidividual recipes and add this Series as `'rating'` column to recipes dataframe. We will use the average rating `'rating'` in the analysis part.
4.  Originally, `'nutrition'` contains nutritions as a list of values. We create individual columns for every unique nutritions.
5.  We create the column `'rating > 3'`, which stores boolean values to indicate whether the rating is greater than 3. If rating is greater than 3, we define it as a high-rating recipe, otherwise a low-rating recipe. This information will be used in our analysis. 
6.  We create the column `'description missing'`, which stores boolean values to indicate whether the description is missing. This information will be used in our missingness analysis.
7. For our Bivariate Analysis, we want to know whether a recipe's calories is high or low. We define a recipe that has at least 400 calores to be a high-calorie recipe. Therefore, we create a column high_calories, which stores boolean values to indicate whether the recipe is a high-calorie recipe or not.

#### Cleaned Dataframe
**Note**: this dataframe shows only relevant columns in the cleaned dataframe instead of the whole dataframe

## Framing the Problem

## Baseline Model

## Final Model

## Fairness Analysis
