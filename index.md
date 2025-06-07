\# Recipe Rating Prediction &#x20;

\_*By Andrew Kim and Kyle Le*\_

\## Introduction

This project explores recipe and user rating data scraped from \[food.com]\([https://www.food.com/](https://www.food.com/)), using data science and machine learning techniques to uncover insights. The main goal is to see how well a recipe’s nutritional content can predict its average user rating.

\*\*Who is this for?\*\* &#x20;

This analysis was originally created as a school project, but it’s designed to be approachable for anyone interested in food, data science, or both—whether you’re a student, employer, or a food enthusiast!

\*\*Central Question:\*\* &#x20;

Can a recipe’s nutritional facts—such as calories, fat, protein, and carbohydrates—predict how well it is rated by users on Food.com?

\*\*Why this matters:\*\* &#x20;

Understanding how nutrition relates to user ratings can be useful for anyone from food companies to home cooks. It can answer questions like: \_Do healthier recipes tend to be rated higher or lower? Are certain nutritional facts associated with more popular recipes?\_ With the help of data science and machine learning, we can look for trends that might otherwise go unnoticed.

\*\*Dataset Details:\*\*

\- \*\*Number of rows (recipes):\*\* The processed dataset has about \*\*23,000\*\* recipes, each paired with ratings from actual users.

\- \*\*Relevant columns:\*\*

&#x20;   \- \`calories\`: Amount of calories per recipe.

&#x20;   \- \`total fat\`: Total fat content.

&#x20;   \- \`sugar\`: Sugar content.

&#x20;   \- \`sodium\`: Sodium content.

&#x20;   \- \`protein\`: Protein content.

&#x20;   \- \`saturated fat\`: Saturated fat content.

&#x20;   \- \`carbohydrates\`: Carbohydrate content.

&#x20;   \- \`average\_rating\`: The average user rating for the recipe (on a scale from just above 0 to 5, allowing for decimals).

These columns represent the key nutritional facts and user feedback (rating) used to explore and answer the project’s central question.

In this project, we use approachable, high-level data science and machine learning methods to both predict ratings and understand the relationship between nutrition and popularity.

\---

\## Data Cleaning and Exploratory Data Analysis

Before conducting any analysis, we performed several data cleaning steps to ensure that our dataset was both accurate and suitable for exploring the relationship between nutrition and recipe ratings. Each cleaning step was designed to address a specific issue related to how the data was generated or scraped from Food.com.

\### 1. Merging and Aligning the Data

The raw dataset consisted of two separate files: &#x20;

\- \*\*RAW\_recipes.csv:\*\* Contained metadata and nutrition facts for each recipe. &#x20;

\- \*\*interactions.csv:\*\* Contained user-submitted ratings for recipes.

These files were joined by matching recipe IDs so that each recipe could be paired with its corresponding ratings. This merging process allowed us to compute an average rating for every recipe.

\### 2. Parsing and Expanding Nutrition Information

The original nutrition facts were stored as a single stringified list in each row (e.g., \`"\[150.0, 10.0, 5.0, ...]"\`). &#x20;

To make this data usable:

\- We converted the string list into an actual Python list of floats.

\- We then split these lists into individual nutrition columns: \`calories\`, \`total fat\`, \`sugar\`, \`sodium\`, \`protein\`, \`saturated fat\`, and \`carbohydrates\`.

\*\*Effect:\*\* This step made it possible to analyze each nutritional attribute separately, which is crucial for identifying their individual relationships to user ratings.

\### 3. Filtering and Averaging Ratings

Each recipe could have multiple user ratings (as individual rows in the interactions file).

\- We grouped ratings by recipe and calculated the \*\*average rating\*\* for each recipe.

\- Ratings of exactly 0 were replaced with \`NaN\`, since a rating of 0 does not make sense on Food.com’s (0, 5] scale and likely represents missing or erroneous data.

\*\*Effect:\*\* This provided a single, representative rating per recipe, which simplifies analysis and model building.

\### 4. Dropping Irrelevant Columns

To focus strictly on the relationship between nutrition and ratings:

\- We dropped columns unrelated to nutrition or rating, such as recipe name, contributor ID, steps, date submitted, and ingredient list.

\*\*Effect:\*\* This reduced noise in the dataset and ensured only relevant features were included in analyses.

\### 5. Additional Notes

\- \*\*Outlier Filtering:\*\* Any additional filtering, such as removing recipes with more than 2500 calories, was performed during the exploratory data analysis step, not as part of initial cleaning.

\- \*\*Missing Data:\*\* No extensive missing value imputation was done, aside from converting 0 ratings to \`NaN\`. Recipes or nutritional facts missing for other reasons were left as-is.

\- \*\*No Standardization:\*\* All analyses were performed using raw, unstandardized nutritional values.

\- \*\*Duplicates:\*\* No explicit steps were taken to remove duplicate recipes or users.

\- \*\*Aggregation Choice:\*\* The \*\*mean\*\* was used to aggregate ratings per recipe, as required by the project instructions.

\---

\### Cleaned DataFrame Head

Below is the head of the cleaned DataFrame, showing the structure of the data used in the analysis:

!\[Cleaned DataFrame Head]\(df\_head.png)

\---

\## Univariate Visualization

!\[Distribution of Calories]\(univariate.png)

The histogram above shows the distribution of calorie content across all recipes in the dataset. &#x20;

Most recipes contain fewer than 500 calories, with a sharp decrease in the number of recipes as calories increase. Very high-calorie recipes are rare, indicating that the majority of recipes on Food.com are relatively moderate in caloric content.

\---

\## Bivariate Visualization

!\[Scatter plot of Calories vs Rating]\(bivariate.png)

This scatter plot displays the relationship between a recipe’s calories and its average user rating. &#x20;

There is no obvious linear trend, but we see that most recipes—regardless of calorie content—tend to receive high ratings. Outliers with extremely high calories exist across different ratings, but the highest density of recipes is concentrated at lower calorie values and higher ratings.

\---

\## Grouped Table

!\[Bar plot of Average Recipe Rating by Calories (500 Calorie Bins)]\(aggregates.png)

This bar chart shows the average user rating for recipes within each calorie range, using uniform bins of 500 calories. &#x20;

The ratings are consistently high across all calorie bins, suggesting that recipes are rated favorably regardless of their calorie content. There is no clear trend that higher or lower calorie recipes are rated better or worse, which indicates that users may value factors other than calorie count when rating recipes.

\---

\## Assessment of Missingness

In our dataset, the primary missing values occur in the `average_rating` column. Specifically, ratings that were exactly 0 were treated as missing (`NaN`) because a rating of 0 does not make sense on Food.com’s (0, 5] scale and likely represents missing or erroneous data.

Based on our understanding of the data collection process, it is possible that some missing ratings are **not missing at random (NMAR)**. For example, a user may be more likely to skip rating a recipe if they did not like it or did not finish making it. This means the likelihood of a rating being missing could depend on the actual (unobserved) rating value itself. As a result, analyses using only the observed ratings might overestimate the average rating.

To determine whether the missingness is truly NMAR, we would need more information—such as user activity logs, reasons for missing ratings, or explicit feedback from users on why they did not rate a recipe. If such data were available, we could potentially reclassify the missingness as **missing at random (MAR)** if we could explain it using other observed variables (like user engagement or recipe difficulty).

---

## Results of Missingness Permutation Tests

To investigate the relationship between missingness in the `review` column and other variables, we performed a permutation test comparing the average `minutes` between recipes with missing reviews and those with non-missing reviews.

The resulting boxplot below shows the distribution of `minutes` required for recipes, separated by whether the review is missing (`true`) or not missing (`false`). While there are some extreme outliers, the vast majority of recipes for both groups cluster at much lower values.

!\[Distribution of Minutes by Review Missingness]\(missingness.png)

**Interpretation:**
The observed difference in average `minutes` between recipes with and without missing reviews was 33.56, with a permutation test p-value of 0.6890. This suggests that there is no statistically significant relationship between the missingness of reviews and the preparation time of recipes. In other words, missing reviews do not appear to be associated with substantially different cooking times in this dataset.

\---

\## Hypothesis Testing

We wanted to investigate whether the average preparation time (\`minutes\`) is different between recipes with missing reviews and those with non-missing reviews.

\*\*Null Hypothesis (\$H\_0\$):\*\* &#x20;

The average preparation time (\`minutes\`) is the same for recipes with missing reviews and those with non-missing reviews.

\*\*Alternative Hypothesis (\$H\_A\$):\*\* &#x20;

The average preparation time (\`minutes\`) is different for recipes with missing reviews compared to those with non-missing reviews.

\*\*Test Statistic:\*\* &#x20;

We used the difference in mean preparation time (\`minutes\`) between the two groups (missing review vs. not missing review) as our test statistic.

\*\*Significance Level (\$\alpha\$):\*\* &#x20;

We set the significance level at 0.05.

\*\*Results:\*\* &#x20;

\- Observed difference in means: 33.56 minutes &#x20;

\- p-value: 0.6890

\*\*Conclusion:\*\* &#x20;

Since the p-value (0.6890) is much greater than our significance level (0.05), we fail to reject the null hypothesis. There is no statistically significant evidence that the average preparation time differs between recipes with missing reviews and those with non-missing reviews in this dataset.

\*\*Justification:\*\* &#x20;

The permutation test is a good choice here because it does not assume normality and directly compares the empirical distributions of the two groups. Using the difference in means as a test statistic aligns with our goal of comparing average preparation times, and a significance level of 0.05 is standard in data analysis.
