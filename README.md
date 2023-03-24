# Calorie Crystal Ball🔮: predict the calories using model building
A project for DSC 80 at UCSD. A fair Prediction Model to predict calories of recipes.
##### project contributors: Wushuang Li, Zixuan Zhao

---

## Framing the Problem
##### Data Cleaning Steps: 
1.  In the getting the data part, we have two dataframes, `'recipes'` and `'interactions'`. Left merge them.
2.  In the merged dataset, fill all ratings of 0 with np.nan. This is **neccessary** because if a recipe is rated 0, it means a reviewer did not enter the rating. So it can be seen as a column containing missing values.
3.  Find the average rating of invidividual recipes and add this Series as `'rating'` column to recipes dataframe. We will use the average rating `'rating'` in the analysis part.
4.  Originally, `'nutrition'` contains nutritions as a list of values. We create individual columns for every unique nutritions.

### Prediction Problem
We plan to perform **regression** to **predict the calories in the recipe**.

We chose calories as the response variable because it is likely to depend heavily on the amount of each nutrition.
- From the information we know: We cleaned nutrition by separating the list into seven columns: calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)

We will use R^2 to evaluate our model because we will build a regression model.

---

## Baseline Model

We build a K-nearest Neighbours Regression model as our baseline model with two quantitative features `sugar (PDV)` and `'carbohydrates (PDV)'` in recipes dataframe
- We chose the  K-nearest Neighbours Regression
Because we are unsure if the true relationship is linear. The results will be at worst slightly inferior to linear regression if the true relationship is linear, but it will be substantially better if the true relationship is non-linear
- For our baseline model, we decided to employ two features, `'sugar (PDV)'` and `'carbohydrates (PDV)'`
We chose `'sugar (PDV)'` and `'carbohydrates (PDV)'` because, at first glance, they look like nutrients that can influence Calories on a high level.
Both features are quantitative, and we didn’t perform any transformation in this stage
After train test split, our mode consistently scores around 0.5 R^2
- It is observed that our baseline model lacks complexity and may cause underfit. Based on R^2, our baseline model is not great and there are definitely lots of room for improvement, but it is not horrible either.

---

## Final Model
- Model Description: we used a ColumnTransformer(to Normalize `'sugar (PDV)'` and `'saturated fat (PDV)'`, and to standardize `'carbohydrates (PDV)'`, `'total fat (PDV)'`, `'protein (PDV)'`) and use KNeighborsRegressor
- With a low R^2 score for test set, we think our model needs more complexity. It was clear more features were needed for a better model. 
- After some research online, we realized that all nutrients listed are important when it comes to calculating calories, besides Sodium, which has no calories at all. Therefore, we ended up including five features: total fat (PDV), sugar (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV).
We noticed that `'sugar (PDV)'` and `'saturated fat (PDV)'` have particularly large outliers and relatively large IQR. We decided to perform normalization with these two features because we worry that the extreme outliers and variability can influence standardization in a negative way. 
- For the other three features, `'carbohydrates (PDV)'`,`'total fat (PDV)'`, `'protein (PDV)'`, we employ standardization. This is because in data generating process: 
  1. We do not see extreme outliers in these three features
  2. By observing the relatively large difference in ranges for these three features, we want to prevent features with wider ranges   from dominating our metric
- We use gridsearchcv to find the best hyperparameter. The best n_neighbor is 2 and the best weight is distance rather than uniform.
- Final model description: Our final model use ColumnTransformer descriped above and KNeighborsRegressor with the best combination of hyperparameters(n_neighbor is 2, weight is distance)
- Performance: Our final model consistently scores over 0.9 R^2 in the test dataset, which is a huge improvement over the baseline model’s 0.5 R^2.
- we visualize the density distribution of predicted values and true values of calories
<iframe src="assets/density1.html" width=620 height=400 frameBorder=0></iframe>
---

## Fairness Analysis
- Choice of Groups:
recipes submitted before 2010 and recipes submitted on and after 2010
- **Question**: does my model perform differently for recipes submitted before 2010 and recipes submitted on and after 2010?
- Null Hypothesis: Our model is fair. Its performance for recipes submitted before and after 2010 is roughly the same, and any differences are due to random chance.
- Alternative hypothesis: Our model is unfair, Its performance for recipes submitted before and after 2010 is different.

#### Evaluation Metric
We decided to use the absolute difference in R^2 as a metric. Since we conduct a two-sided test (our alternative hypothesis is simply about whether two distributions are different. We use R^2 because we built a regression model. 

#### Permutation Test
- To see difference in two distributions, we perform a permutation test. We choose a significane level of 0.05.
- To start with, we train test split and train our model with the train data.
- Then we separate the test data into two groups and obtain the observed absolute difference of R^2 between the two groups
We repeatedly shuffle the test data and obtain a list of differences in R^2 between the two groups.
- Calculate the **p-value** by comparing the list to our observed value.
We got a large p-value of 0.5, which is greater than our significance level. We fail to reject the null hypothesis. 
- Hence, we conclude that it seems like our model is fair.
- Visualization of permutation test
<iframe src="assets/hist1.html" width=620 height=400 frameBorder=0></iframe>
