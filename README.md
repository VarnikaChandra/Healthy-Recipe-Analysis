# Recipes and Ratings Analysis 🍽️  
_A project for EECS 398 at the University of Michigan_  
**By: Varnika Chandra – varnikac@umich.edu**  

## Introduction  
This project explores the question: What makes a recipe “healthy”? Using a dataset scraped from Food.com, which includes thousands of recipes and user reviews from 2008 onward, I aim to understand which features—such as nutritional content, preparation time, and number of ingredients—are most associated with user satisfaction on recipes labeled as “healthy.” The dataset includes two key files: RAW_recipes.csv, which contains 83,782 recipes with columns like name, minutes, tags, nutrition, n_steps, and n_ingredients; and RAW_interactions.csv, which includes 731,927 user interactions with columns such as user_id, recipe_id, rating, and review. With the rise of popular diets promoted through social media—such as anti-inflammatory, keto, and high-protein—this analysis aims to identify the traits that define healthy, well-rated recipes. The findings could inform health-conscious users, or work as a recommendation system on what factors contribute to perceived healthiness and satisfaction.

## Data Cleaning and Exploratory Data Analysis  

To prepare the dataset for analysis, I performed numerous cleaning and transformation steps across both the recipes and user interaction files:

- Normalized all column names by converting them to lowercase and removing special characters.
- Removed duplicate ratings (same user and recipe) and filtered out invalid ratings (where score = 0).
- Replaced `minutes = 0` with `NaN`, then filled missing values using the median of recipes under 48 hours.
- Removed extreme outliers where preparation time exceeded 48 hours.
- Filled missing descriptions with `"NA"` and extracted `year_submitted` from the `submitted` date column.
- Parsed the `nutrition` column into individual components:  
  `calories`, `total_fat`, `sugar`, `sodium`, `protein`, `saturated_fat`, `carbohydrates`, and filled missing values with column medians.
- Merged in user rating statistics to add `avg_rating` and `num_reviews` per recipe.

### Engineered Features

- `calories_per_ingredient`: a proxy for recipe density.
- `is_quick`: whether a recipe takes under 30 minutes.
- `is_popular`: whether a recipe has ≥ 100 reviews.
- `protein_per_calorie` and `sugar_per_calorie`: for nutrient efficiency.
- `is_health_tagged`: flag for recipes with tags like `"healthy"`, `"low-fat"`, or `"gluten-free"`.

---

### Preview of Cleaned Dataset (`recipes_df.head()`)

| name                          | recipe_id | minutes | n_ingredients | calories | avg_rating | is_health_tagged |
|-------------------------------|-----------|---------|----------------|----------|-------------|------------------|
| brownies in the world         | 333281    | 40      | 9              | 138.4    | 4.0         | 0                |
| 1 in canada chocolate chip... | 453467    | 45      | 11             | 595.1    | 5.0         | 0                |
| 412 broccoli casserole        | 306168    | 40      | 9              | 194.8    | 4.0         | 0                |
| millionaire pound cake        | 286009    | 120     | 7              | 878.3    | 5.0         | 0                |
| 2000 meatloaf                 | 475785    | 90      | 13             | 267.0    | 4.0         | 0                |

🔗 [Click here to view full dataset preview (HTML)](preview_table.html)

### Univariate Analysis
**Protein Levels Across Healthy Recipes**
<iframe
  src="assets/protein_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
This histogram displays the distribution of protein content in health-tagged recipes. The distribution is right-skewed, with most recipes containing under 20 grams of protein, suggesting that while high-protein options exist, the majority of "healthy" recipes on the platform tend to be lower in protein.


## Framing a Prediction Problem  
[Describe the classification problem: predicting whether a recipe will be labeled as "healthy".  
Include the target variable and input features, and your reasoning.]

## Baseline Model  
[Describe your baseline pipeline, which features you used, what model, and how it performed (e.g., F1 score, accuracy).  
Include the classification report as a code block.]

## Final Model  
[Describe the final model, the features you engineered, why you engineered them, the model used, and the GridSearchCV results.  
Include a confusion matrix plot using Plotly as an iframe and a summary of performance improvement.]
