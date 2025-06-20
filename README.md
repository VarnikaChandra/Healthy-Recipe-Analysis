# Recipes and Ratings Analysis 🍽️  
_A project for EECS 398 at the University of Michigan_  
**By: Varnika Chandra – varnikac@umich.edu**  

## Introduction  
[This project explores the question: What makes a recipe “healthy”? Using a dataset scraped from Food.com, which includes thousands of recipes and user reviews from 2008 onward, I aim to understand which features—such as nutritional content, preparation time, and number of ingredients—are most associated with user satisfaction on recipes labeled as “healthy.” The dataset includes two key files: RAW_recipes.csv, which contains 83,782 recipes with columns like name, minutes, tags, nutrition, n_steps, and n_ingredients; and RAW_interactions.csv, which includes 731,927 user interactions with columns such as user_id, recipe_id, rating, and review. With the rise of popular diets promoted through social media—such as anti-inflammatory, keto, and high-protein—this analysis aims to identify the traits that define healthy, well-rated recipes. The findings could inform health-conscious users, or work as a recommendation system on what factors contribute to perceived healthiness and satisfaction.]

## Data Cleaning and Exploratory Data Analysis  
[To prepare the dataset for analysis, I performed numerous cleaning and transformation steps across both the recipes and user interaction files. First, I normalized all column names by converting them to lowercase and removing special characters to ensure consistency. In the interactions dataset, I removed duplicate entries where the same user rated the same recipe multiple times, and filtered out invalid ratings where a score of 0 represented missing data.
In the recipes dataset, I replaced preparation times of 0 minutes with NaN and filled these missing values using the median time for reasonably timed recipes (less than 48 hours). I also removed extreme outliers with durations exceeding 48 hours. Missing values in the recipe descriptions were filled with "NA" to preserve textual consistency, and submission dates were converted into datetime objects with a new column (year_submitted) extracted to support temporal analysis.
One of the most important transformations involved expanding the nutrition column. I parsed this into separate columns for calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates. Any missing nutritional data was imputed using the median of each column.
I also merged the recipes dataset with a ratings summary, adding two new columns: avg_rating and num_reviews for each recipe. To enrich the dataset, I engineered several new features, such as:

-calories_per_ingredient: a proxy for density
-is_quick: a binary flag for recipes under 30 minutes
-is_popular: based on review count (≥ 100)
-protein_per_calorie and sugar_per_calorie: useful for identifying nutrient density

Finally, the tags column was parsed into lists, and a new column is_health_tagged was created to flag recipes containing health-related tags like "healthy", "low-fat", or "gluten-free". This allowed me to isolate a subset of over 20,000 health-focused recipes for deeper analysis.
Below is a preview of the cleaned dataset:
### Preview of Cleaned Dataset (`recipes_df.head()`)

| name                          | recipe_id | minutes | n_ingredients | calories | avg_rating | is_health_tagged |
|-------------------------------|-----------|---------|----------------|----------|-------------|------------------|
| brownies in the world         | 333281    | 40      | 9              | 138.4    | 4.0         | 0                |
| 1 in canada chocolate chip... | 453467    | 45      | 11             | 595.1    | 5.0         | 0                |
| 412 broccoli casserole        | 306168    | 40      | 9              | 194.8    | 4.0         | 0                |
| millionaire pound cake        | 286009    | 120     | 7              | 878.3    | 5.0         | 0                |
| 2000 meatloaf                 | 475785    | 90      | 13             | 267.0    | 4.0         | 0                |
🔗 [Click here to view full dataset preview (HTML)](preview_table.html)
]

## Framing a Prediction Problem  
[Describe the classification problem: predicting whether a recipe will be labeled as "healthy".  
Include the target variable and input features, and your reasoning.]

## Baseline Model  
[Describe your baseline pipeline, which features you used, what model, and how it performed (e.g., F1 score, accuracy).  
Include the classification report as a code block.]

## Final Model  
[Describe the final model, the features you engineered, why you engineered them, the model used, and the GridSearchCV results.  
Include a confusion matrix plot using Plotly as an iframe and a summary of performance improvement.]
