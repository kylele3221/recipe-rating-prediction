## Introduction

This project explores recipe and user rating data scraped from [food.com](https://www.food.com/), using data science and machine learning techniques to uncover insights. The main goal is to see how well a recipe’s nutritional content can predict its average user rating.

**Who is this for?**
This analysis was originally created as a school project, but it’s designed to be approachable for anyone interested in food, data science, or both—whether you’re a student, employer, or a food enthusiast!

**Central Question:**
Can a recipe’s nutritional facts—such as calories, fat, protein, and carbohydrates—predict how well it is rated by users on Food.com?

**Why this matters:**
Understanding how nutrition relates to user ratings can be useful for anyone from food companies to home cooks. It can answer questions like: *Do healthier recipes tend to be rated higher or lower? Are certain nutritional facts associated with more popular recipes?* With the help of data science and machine learning, we can look for trends that might otherwise go unnoticed.

**Dataset Details:**

* **Number of rows (recipes):**
  The processed dataset has about **23,000** recipes, each paired with ratings from actual users.
* **Relevant columns:**

  * `calories`: Amount of calories per recipe.
  * `total fat`: Total fat content.
  * `sugar`: Sugar content.
  * `sodium`: Sodium content.
  * `protein`: Protein content.
  * `saturated fat`: Saturated fat content.
  * `carbohydrates`: Carbohydrate content.
  * `average_rating`: The average user rating for the recipe (on a scale from just above 0 to 5, allowing for decimals).

These columns represent the key nutritional facts and user feedback (rating) used to explore and answer the project’s central question.

In this project, we use approachable, high-level data science and machine learning methods to both predict ratings and understand the relationship between nutrition and popularity.
