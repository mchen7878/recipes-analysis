# Wondrous Ways to Ruin Dinner: The Secret to getting star ratings!

---

## 1. Introduction

Home-cooked meals and recipes have become indispensable in today's culinary landscape (and economic climate). For our project, we've selected the recipes and ratings dataset as our playground. From extensive research (mostly binge-watching reels) in the digital cooking realm, we've noticed that certain cooking "hacks" have become predictable clichés: *"Season your pasta water like the ocean!" "Never enough garlic!"* Following these tips guarantees applause and validation from your audience.

Yet, a quick exploration of our dataset reveals something intriguingly counterintuitive: high ratings (4 and 5 stars) vastly outnumber the elusive 1-star disasters. Surprisingly, this rarity makes the one-star rating oddly prestigious. It takes genuine talent and creativity to fail spectacularly in a sea of predictably good recipes.

This brings us to our provocative question: how exactly can you create the worst recipes possible?

Our dataset comprises user-submitted recipes and reviews, totaling approximately 731,927 rows. Relevant columns include:

rating: The numerical user rating assigned to recipes, ranging from 0 ("burnt toast") to 5 ("Michelin star quality").

review: User-written reviews that provide textual feedback on recipes, offering rich insights into culinary disasters and triumphs alike.

Together, these columns help us unravel the mystery behind crafting recipes that earn the rarest—and perhaps most intriguing—distinction: the one-star masterpiece.




---

## 2. Cleaning and EDA

Something peculiar from the data was the presednce of 0-star ratings. After some investigation, it turns out that the 0-star ratings were usually a result of the user abstaining from rating the recipe or a misinput, meaning these values have little numerical significance with respect to our purposes. The 1-star ratings on the other hand, are as you expect.

<iframe
src="assets/rating_0.html"
width="800"
height="600"
frameborder="0"
></iframe>

<iframe
src="assets/rating_1.html"
width="800"
height="600"
frameborder="0"
></iframe>

Next, since we have two datasets, the recipes and the ratings, we will now combine them in order to do analysis between the ratings and the components of the recipes. Here is our new dataset. 

<iframe
src="assets/ratings_and_recipes.html"
width="800"
height="600"
frameborder="0"
></iframe>



### Univariate Analysis

The historgram below shows the rarity of 1-star ratings. It shows the number of recipes that received each star rating. This likely can be explained by self-selection, where people will only publish good recipes.

<iframe
src="assets/distribution_ratings.html"
width="800"
height="600"
frameborder="0"
></iframe>


### Bivariate Analysis

The box plot below shows the word count for the reviews at different rating levels. It appears that for the most part, the word counts for each rating level is relatively the same but the number of positive outliers, long reviews that is, is greater for higher star ratings. This may be a result of better recipes having more to talk about, or perhaps botting...

<iframe
src="assets/rating_vs_length.html"
width="800"
height="600"
frameborder="0"
></iframe>

### Interesting Aggregates

From our findings, it seems that generally recipes with a larger number of steps had worst ratings, though there is nothing concerete. Further investigation is required...

<iframe
src="assets/pivot_steps_rating.html"
width="800"
height="600"
frameborder="0"
></iframe>

### Imputation

Looking at which columns of our dataframe are acrtually missing values, it seems to be mostly just the 'description' and 'review' columns. Besides that, there seems to be little numerical values that actually need to be imputed.

<iframe
src="assets/missing_values.html"
width="800"
height="600"
frameborder="0"
></iframe>


---

## 3. Prediction Problem

Our prediction problem can be modeled as a binary classification problem, where a new recipe will receive a “low” rating (1, 2, or 3 stars) versus a “high” rating (4 or 5 stars). Our response variable will be a new column "is_low_rating" which is essentially whether or not ['rating'] <= 3, with 1 indicating a low (1–3) star outcome and 0 indicating a high (4–5) star outcome. We chose a binary classifier because our project’s theme is flagging anything likely to fall short of excellence—by lumping the middle‑of‑the‑road (3 stars) in with outright flops, we ensure the model learns to catch any recipe that are deserving of a low rating.

All features we use—total cook + prep time (minutes), instruction count (n_steps), ingredient count (n_ingredients), nutrition facts (calories, fat, carbs, protein), category tags (tags), and description length—are available before publication, so our model respects the “time of prediction” constraint.

Because the low‑rating class makes up less than half of our data, we’ll evaluate performance primarily using the F₁‑score on the is_low_rating class.

## 4. Baseline Model

## 5. Final Model

---
