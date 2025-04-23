
---

## 1. Introduction

For this analysis, we will be using the Ratings and Recipes Dataset from food.com. 

- RAW_recipes.csv, which contains relevant recipe information like time to cook, ingredients, nutrtional value, etc.
- RAW-interations.csv, which contains user's ratings and reviews of the recipes

After merging the two datasets together, our combined dataset of ratings and recipes had 234,429 rows and 17 columns.

We plan on investigating the relationship between how the contents of a recipe may be related to how long it takes to cook them. This is significant as it will provide to readers a better intuition for the lengths of certain recipes just by getting a rough glance of the ingredients. 

We will mostly be concerned with the following columns:
- minutes: the time it takes for a recipe
- ingredients: the various ingredients of the recipe
- n_ingredients: the number of ingredients in the recipe
- nutrition: the nutritional content of the recipe
- rating: the rating the recipe received

---

## 2. Cleaning and EDA
Before creating any models to answer our question, we will need to clean the data and do some exploratory data analysis in order to obtain a better understanding of the data

### Step 1: Merge the Datasets
As prefaced in the introduction, we started off with two datasets, one for the recipes, the other for the ratings. In order to have an easier time working between these two datasets, we merged them using a left join on the 'recipe_id' of recipes and interations respectively, meaning that each recipe will remain in the merged dataset regardless of whether or not it had any interactions.

### Step 2: Drop 0 Star Ratings
Something peculiar from the data was the presence of 0-star ratings. After some investigation, it turns out that the 0-star ratings were usually a result of the user abstaining from rating the recipe or a misinput, meaning these values have little numerical significance with respect to our purposes. We used .drop() with a boolean mask to drop these columns so it won't be considered in our analysis.

The reviews for the recipes with 0-star ratings.
<iframe
src="assets/0rating_top7.html"
width="800"
height="400"
frameborder="0"
></iframe>

### Step 3: Handling Multiple Ratings per Review
Certain reviews had multiple ratings. In order to handle this, we used groupby on recipe id and tranformed the rating value to be the mean rating. This makes it so each recipe will have only a single rating associated with it.

### Step 4: Extracting Values from Nutrition Column
Previously, the dataset had a list for the 'Nutrition' column. In order to extract the numerical information from it, we split the list into seperate columns and concatenated each of them onto our dataframe. Then, we dropped the 'Nutrition' column as it was no longer necessary.

### Step 5: Remove Outliers in Prep Time
In our investigation, certain recipes had way longer preparation times than the rest of the data (the max of the minutes column was 1051200). We used the IQR 'minutes' column to create an upper bound from which we remove any outliers beyond that.

Below an overview of the cleaned_table.

<iframe
src="assets/headcleaned_table.html"
width="800"
height="400"
frameborder="0"
></iframe>

Something interesting we found in our investaigation of the data was the oversaturation of higher star ratings, The historgram below shows the distribution of ratings amongst the recipes. Notably, the little diversity in this data will indicate that we will need more features than just 'rating' to accurately predict 'minutes'.

<iframe
src="assets/distribution_ratings.html"
width="800"
height="400"
frameborder="0"
></iframe>

Taking a closer look with a scatter plot, there doesn't appear to be a high correlation between average star rating and the preparation time. Perhaps a reason for the cluster in the top left is the high number of high star ratings as found earlier and the fact that most recipe have slower preparation times.

<iframe
src="assets/rating_vs_prep.html"
width="800"
height="400"
frameborder="0"
></iframe>

### Interesting Aggregates

Pulling in some other features, we created a pivot table that shows relationship between number of ingredients and the minutes it took to prepare the number of steps associated with it. Notably, the recipes with a 'medium' number of ingredients seemed to require less time to prepare. As one would expect though, there seems to be a positive relationship between the number of steps and the number of ingredients. 

<iframe
src="assets/ningred_min_nstep.html"
width="800"
height="300"
frameborder="0"
></iframe>

### Imputation

Looking at which columns of our dataframe are actually missing values, it seems to be mostly just the 'description' and 'review' columns. Besides that, there seems to be little numerical values that actually need to be imputed.

<iframe
src="assets/missing_values.html"
width="800"
height="300"
frameborder="0"
></iframe>

---

## 3. Prediction Problem

For our analysis, we will build a regression model that predicts the 'minutes' it takes for a recipe using only with information that can be collected from only a quick glance, so things like 'n_ingredients', 'rating', etc. As stated, our response variable will be 'minutes', and finding out which heuristics provide the best insight into how long a recipe may take and will be useful for readers in a hurry reading recipes that don't already have it listed. Our evaluation metric will be MSE, mean squared error, to see how well our regression model performs. MSE is suitable as it will penalize larger errors more heavily, and so will avoid vast under/overestimates of the time it takes to prepare a recipe.

## 4. Baseline Model

We settled on a Ridge regression model that takes in n_steps, n_ingredients, and mean_rating. All three of these features are purely quantative. For our pipeline, we used StandardScaler to center and normalize each feature. We then ran a 3‑fold grid search over six regularization strengths [0.001, 0.01,  1, 10, 100, 1000] as a hyperparameter. The MSE was 465.32.

While this MSE may seem high, given that the recipes span can span from a few minutes to many hours, having an average error of around 21.6 minutes isn't so bad. Also, from the grid search,  an alpha of 100 for the regularization strength performed the best. This makes sense as the features are noisy and can vary a lot from recipe to recipe. A higher regularization strength like 100 can help 'even' this out.


## 5. Final Model

For our final model, we feature engineered two new features that we suspected might capture some aspects of recipe prep time. For the first feature we computed 'fat_per_calorie' (total_fat_pdv ÷ calories × 100) to measure recipe richness: high‐fat recipes often require extra preparation steps—melting, whisking, careful temperature control—so this ratio could correlate with longer cook times. For the second feature, we flagged whether or not a recipe 'contains meat' via a regex search over the ingredient list. Our reasoning was that recipes with meat generally need additional handling compared to vegetarian dishes, so a boolean indicator should help explain why some recipes take longer.

We built upon the same modeling algorithm as the baseline model. For the regularization strength of our Ridge model, we used GridSearchCV to iterate through the same 6 alphas [0.001, 0.01,  1, 10, 100, 1000] and found that an alpha of 10 was actually the best parameter. This indicates that some of the variance from the previous model that needed a higher regularization strength to prevent overfitting was actually accounted for by our new features. Still though, an alpha of 10 is needed to prevent overfitting to the natural noise of the data, and so it makes sense why a lower regularization strength wasn't chosen. This time around, the model's MSE fell to 459.37. Because the added features represent meaningful aspects of cooking complexity, they allowed the ridge estimator to explain variance that the baseline features missed, yielding a tighter fit to actual minutes.


Created by: Michael Chen and Edison Zhou
---
