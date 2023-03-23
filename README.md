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
1. We cleaned nutrition by separating the list into seven columns: calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)
2. We plan to perform regression to predict the calories in the recipe
3. We chose calories as the response variable because it is likely to depend heavily on the amount of each nutrition.
4. We will use R^2 to evaluate our model.

## Baseline Model

### choose regression
We chose the Knn regression
Because we are unsure if the true relationship is linear. The results will be at worst slightly inferior to linear regression if the true relationship is linear, but it will be substantially better if the true relationship is non-linear
### choose features
For our baseline model, we decided to employ two features, sugar (PDV) and carbohydrates (PDV)
We chose Sugar and carbohydrate because, at first glance, they look like nutrients that can influence Calories on a high level.
Both are quantitative, and we didn’t perform any transformation in this stage
After train test split, our mode consistently scores around 0.5 R^2
Our baseline model is not great and there are definitely lots of room for improvement, but it is not horrible either.


## Final Model
- It was clear more features were needed for a better model. After some research online, we realized that all nutrients listed are important when it comes to calculating calories, besides Sodium, which has no calories at all. Therefore, we ended up including five features: total fat (PDV), sugar (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV).
- We noticed that sugar (PDV) and saturated fat (PDV) have particularly large outliers and relatively large IQR. We decided to perform normalization with these two features because we worry that the extreme outliers and variability can influence standardization in a negative way. For the other three features, we employ standardization.
- We use gridsearchcv to find the best hyperparameter. The best n_neighbor is 2 and the best weight is distance rather than uniform.
- Our final model consistently scores over 0.9 R^2 in the test dataset, which is a huge improvement over the baseline model’s 0.5 R^2.

## Fairness Analysis
### Choice of groups:
recipes submitted before 2010 and recipes submitted on and after 2010
Question: does my model perform differently for recipes submitted before 2010 and recipes submitted on and after 2010?
Null Hypothesis: Our model is fair. Its performance for recipes submitted before and after 2010 is roughly the same, and any differences are due to random chance.
Alternative hypothesis: Our model is unfair, Its performance for recipes submitted before and after 2010 is different.

### Evaluation Metric
We decided to use the absolute difference in R^2 as a metric. Since we conduct a two-sided test ( our alternative hypothesis is simply about whether two distributions are different. We use R^2 because we built a regression model. 

### Permutation Test
To see difference in two distributions, we perform a permutation test. We choose a significane level of 0.05.
To start with, we train test split and train our model with the train data.
Then we separate the test data into two groups and obtain the observed absolute difference of R^2 between the two groups
We repeatedly shuffle the test data and obtain a list of differences in R^2 between the two groups.
Calculate the p-value by comparing the list to our observed value.
Got a large p-value of 0.5, which is greater than our significance level. We fail to reject the null hypothesis. 
Hence, we conclude that it seems like our model is fair.
