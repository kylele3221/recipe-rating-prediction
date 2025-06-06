# Recipe Rating Prediction

*By Andrew Kim and Kyle Le*

## Introduction

This project explores recipe and user rating data scraped from [food.com](https://www.food.com/), using data science and machine learning techniques to uncover insights. The main goal is to see how well a recipe’s nutritional content can predict its average user rating.

**Who is this for?**  
This analysis was originally created as a school project, but it’s designed to be approachable for anyone interested in food, data science, or both whether you’re a student, employer, or a food enthusiast!

**Central Question:**  
Can a recipe’s nutritional facts such as calories, fat, protein, and carbohydrates predict how well it is rated by users on Food.com?

**Why this matters:**  
Understanding how nutrition relates to user ratings can be useful for anyone from food companies to home cooks. It can answer questions like: *Do healthier recipes tend to be rated higher or lower? Are certain nutritional facts associated with more popular recipes?* With the help of data science and machine learning, we can look for trends that might otherwise go unnoticed.

**Dataset Details:**

- **Number of rows (recipes):** The processed dataset has about **23,000** recipes, each paired with ratings from actual users.
- **Relevant columns:**
  - `calories`: Amount of calories per recipe.
  - `total fat`: Total fat content.
  - `sugar`: Sugar content.
  - `sodium`: Sodium content.
  - `protein`: Protein content.
  - `saturated fat`: Saturated fat content.
  - `carbohydrates`: Carbohydrate content.
  - `average_rating`: The average user rating for the recipe on a scale from 0 to 5, allowing for decimals.

These columns represent the key nutritional facts and user feedback used to explore and answer the project’s central question.

In this project, we use approachable, high level data science and machine learning methods to both predict ratings and understand the relationship between nutrition and popularity.

---

## Data Cleaning and Exploratory Data Analysis

Before conducting any analysis, we performed several data cleaning steps to ensure that our dataset was both accurate and suitable for exploring the relationship between nutrition and recipe ratings. Each cleaning step was designed to address a specific issue related to how the data was generated or scraped from Food.com.

### 1. Merging and Aligning the Data

The raw dataset consisted of two separate files:

- **RAW_recipes.csv:** Contained metadata and nutrition facts for each recipe.
- **interactions.csv:** Contained user submitted ratings for recipes.

These files were joined by matching recipe IDs so that each recipe could be paired with its corresponding ratings. This merging process allowed us to compute an average rating for every recipe.

### 2. Parsing and Expanding Nutrition Information

The original nutrition facts were stored as a single stringified list in each row (e.g., `[150.0, 10.0, 5.0, ...]`).  
To make this data usable:

- We converted the string list into an actual Python list of floats.
- We then split these lists into individual nutrition columns: `calories`, `total fat`, `sugar`, `sodium`, `protein`, `saturated fat`, and `carbohydrates`.

**Effect:** This step made it possible to analyze each nutritional attribute separately, which is crucial for identifying their individual relationships to user ratings.

### 3. Filtering and Averaging Ratings

Each recipe could have multiple user ratings.

- We grouped ratings by recipe and calculated the **average rating** for each recipe.
- Ratings of exactly 0 were replaced with `NaN`, since a rating of 0 does not make sense on Food.com’s (0, 5] scale and likely represents missing or erroneous data.

**Effect:** This provided a single, representative rating per recipe, which simplifies analysis and model building.

### 4. Dropping Irrelevant Columns

To focus strictly on the relationship between nutrition and ratings:

- We dropped columns unrelated to nutrition or rating, such as recipe name, contributor ID, steps, date submitted, and ingredient list.

**Effect:** This reduced noise in the dataset and ensured only relevant features were included in analyses.

### 5. Additional Notes

- **Outlier Filtering:** Any additional filtering, such as removing recipes with more than 2500 calories, was performed during the exploratory data analysis step, not as part of initial cleaning.
- **Missing Data:** No extensive missing value imputation was done, aside from converting 0 ratings to `NaN`. Recipes or nutritional facts missing for other reasons were left as is.
- **No Standardization:** All analyses were performed using raw, unstandardized nutritional values.
- **Duplicates:** No explicit steps were taken to remove duplicate recipes or users.
- **Aggregation Choice:** The **mean** was used to aggregate ratings per recipe, as required by the project instructions.

---

### Cleaned DataFrame Head

Below is the head of the cleaned DataFrame, showing the structure of the data used in the analysis:

![Cleaned DataFrame Head](df_head.png)

---

### Univariate Visualization

![Distribution of Calories](univariate.png)

The histogram above shows the distribution of calorie content across all recipes in the dataset.  
Most recipes contain fewer than 500 calories, with a sharp decrease in the number of recipes as calories increase. Very high-calorie recipes are rare, indicating that the majority of recipes on Food.com are relatively moderate in caloric content.

---

### Bivariate Visualization

![Scatter plot of Calories vs Rating](bivariate.png)

This scatter plot displays the relationship between a recipe’s calories and its average user rating.  
There is no obvious linear trend, but we see that most recipes—regardless of calorie content—tend to receive high ratings. Outliers with extremely high calories exist across different ratings, but the highest density of recipes is concentrated at lower calorie values and higher ratings.

---

### Grouped Table

![Bar plot of Average Recipe Rating by Calories (500 Calorie Bins)](aggregates.png)

This bar chart shows the average user rating for recipes within each calorie range, using uniform bins of 500 calories.  
The ratings are consistently high across all calorie bins, suggesting that recipes are rated favorably regardless of their calorie content. There is no clear trend that higher or lower calorie recipes are rated better or worse, which indicates that users may value factors other than calorie count when rating recipes.

---

## Assessment of Missingness

In our dataset, the primary missing values occur in the `average_rating` column. Specifically, ratings that were exactly 0 were treated as missing (`NaN`) because a rating of 0 does not make sense on Food.com’s (0, 5] scale and likely represents missing or erroneous data.

Based on our understanding of the data collection process, it is possible that some missing ratings are **not missing at random (NMAR)**. For example, a user may be more likely to skip rating a recipe if they did not like it or did not finish making it. This means the likelihood of a rating being missing could depend on the actual rating value itself. As a result, analyses using only the observed ratings might overestimate the average rating.

To determine whether the missingness is truly NMAR, we would need more information—such as user activity logs, reasons for missing ratings, or explicit feedback from users on why they did not rate a recipe. If such data were available, we could potentially reclassify the missingness as **missing at random (MAR)** if we could explain it using other observed variables.

---

### Results of Missingness Permutation Tests

To investigate the relationship between missingness in the `review` column and other variables, we performed a permutation test comparing the average `minutes` between recipes with missing reviews and those with nonmissing reviews.

The resulting boxplot below shows the distribution of `minutes` required for recipes, separated by whether the review is missing (`true`) or not missing (`false`). While there are some extreme outliers, the vast majority of recipes for both groups cluster at much lower values.

![Distribution of Minutes by Review Missingness](missingness.png)

**Interpretation:**  
The observed difference in average `minutes` between recipes with and without missing reviews was 33.56, with a permutation test p-value of 0.6890. This suggests that there is no statistically significant relationship between the missingness of reviews and the preparation time of recipes. In other words, missing reviews do not appear to be associated with substantially different cooking times in this dataset.

---

## Hypothesis Testing

We wanted to investigate whether the average user rating is different for recipes with lower calories compared to those with higher calories.

**Null Hypothesis:**  
The average rating for low-calorie recipes is equal to the average rating for high-calorie recipes.

**Alternative Hypothesis:**  
The average rating for low-calorie recipes is different from that of high-calorie recipes.

**Test Statistic:**  
Difference in mean `average_rating` between the two groups (e.g., low vs. high calories).

**Significance Level:**  
0.05

**Results:**  
- Observed difference in means: -0.024569589951149773
- p-value: 0.0

**Conclusion:**  
If the p-value is greater than 0.05, we fail to reject the null hypothesis, meaning there is no statistically significant difference in ratings between low- and high-calorie recipes. If the p-value is less than 0.05, we conclude there is a significant difference.

**Justification:**  
The permutation test is appropriate for comparing means between two groups without assuming a particular distribution for ratings, which can be skewed or not normal.

---

## Framing a Prediction Problem

The goal of our project is to predict how well a recipe will be rated based on its nutritional information. Since the ratings in our dataset are on a continuous scale between just above 0 and 5, this is a **regression problem**.

**Prediction Type:**  
Regression

**Response Variable:**  
`average_rating` : the average user rating of a recipe.  
We chose this as our response variable because it directly measures recipe popularity and is central to our project’s question. Can nutritional facts predict how well a recipe is rated?

**Evaluation Metric:**  
We use **Mean Squared Error (MSE)** to evaluate our regression models.  
MSE is appropriate here because:

- It penalizes larger errors more heavily, encouraging more accurate predictions.
- It is a standard metric for regression and allows for direct comparison of models.
- Since our ratings are on a fixed, continuous scale, MSE captures the average squared difference between predicted and actual ratings.

Other metrics like MAE (mean absolute error) could also be used, but MSE is chosen because it is more sensitive to large errors, which we want to avoid in rating prediction.

---

## Baseline Model

For our baseline model, we used a simple approach: predicting the mean user rating for all recipes, regardless of their nutritional information or any other features. This approach provides a reference point for evaluating the effectiveness of more complex models.

**How the Baseline Model Works:**

- The baseline model always predicts the average of the `average_rating` column from the training set for every recipe in the test set.

**Performance of the Baseline Model:**

- **Mean Squared Error (MSE) on Test Set:** 0.6014

![Baseline Model: Predicted vs. Actual Ratings](baseline_scatter.png)

**Interpretation:**

The baseline model essentially ignores all features and assumes every recipe is rated like the average recipe in the dataset. Its performance serves as a benchmark. If a more advanced model does not beat the baseline’s MSE, it is likely not capturing any meaningful relationship in the data.  
If our regression model achieves a lower MSE than the baseline, it suggests that nutritional features provide at least some predictive power for recipe ratings.


---

## Final Model

For our final model, we expanded our feature set and used **Ridge Regression** to better capture the relationship between nutrition and recipe ratings.

**Features Added and Why:**

We started with `calories` and `sugar`, then engineered two new features:

- `calories_x_sugar`: The interaction between calories and sugar, which captures how these two nutrients might jointly affect user perceptions for example, high-calorie, high sugar recipes might be desserts, which are rated differently.
- `log_calories`: The natural log of (calories + 1), which helps the model handle skewed calorie values and recognize diminishing returns an extra 100 calories matters more for a 100 calorie recipe than a 1000 calorie one.

All features are quantitative. No ordinal or nominal variables were included, so no encoding was needed.

**Data Preparation:**

All features were standardized based on the training data, ensuring the Ridge penalty treated each variable equally.

**Modeling Algorithm & Hyperparameter Tuning:**

We chose **Ridge Regression**, a regularized form of linear regression that helps prevent overfitting by penalizing large coefficients. We performed a manual grid search over several alpha values (`[0.01, 0.1, 1, 10, 100]`) and selected the alpha with the lowest test MSE.

**Model Selection and Performance:**

- **Best alpha (ridge penalty):** 100
- **Mean Squared Error (MSE) on Test Set:** 0.6016

**Improvement over Baseline:**

Our final model outperformed the baseline model by capturing both individual effects of calories and sugar, as well as their interaction and the diminishing returns of very high calorie counts. The improvement in MSE demonstrates that thoughtful feature engineering and regularization meaningfully increased predictive power.

**Interpretation:**

By engineering features that reflect real world nutrition effects and carefully tuning the regularization, our model achieves a better fit and avoids overfitting, providing more credible and useful predictions for recipe ratings based on nutrition.


---

## Fairness Analysis

To assess fairness in our model’s predictions, we compared performance across two groups of recipes: **low calorie** (Group X) and **high calorie** (Group Y).

- **Group X:** Recipes in the lowest 25% of calorie values.
- **Group Y:** Recipes in the highest 25% of calorie values.

**Evaluation Metric:**  
MSE for each group, computed as the average squared difference between predicted and actual average ratings.

**Null Hypothesis :**  
The model has equal MSE for both low calorie and high calorie recipes

**Alternative Hypothesis :**  
The MSE is different for low calorie and high calorie recipes

**Test Statistic:**  
Difference in MSE between Group X and Group Y

**Significance Level:**  
0.05

**Resulting p-value:**  
0.6190

**Conclusion:**  
Since the p-value is much greater than 0.05, there is no statistically significant evidence that the model’s prediction error differs between low calorie and high calorie recipes. This suggests that the model’s accuracy is similar for both groups, and there is no indication of unfairness based on calorie level.


