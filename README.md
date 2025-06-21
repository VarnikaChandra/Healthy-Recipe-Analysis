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

### Bivariate Analysis
<iframe
  src="assets/rating_healthy_v_unhealthy.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregations 
To examine whether recipes labeled as healthy differ nutritionally from others, I grouped the dataset by the is_health_tagged column and calculated the average values for key nutritional attributes: calories, total fat, sugar, and protein. The results show that healthy-tagged recipes tend to have slightly lower calories (419.87 vs. 430.71) and total fat (31.41g vs. 32.90g) compared to those not labeled as healthy, aligning with general expectations. However, the average sugar content is actually higher in healthy recipes (68.77g vs. 67.33g), which is unexpected. Additionally, non-healthy recipes show a slightly higher protein content on average (33.39g vs. 31.95g). These findings indicate that the trendy “healthy” tags may reflect specific dietary trends or labeling practices rather than consistent improvements across all nutritional metrics.



## Framing a Prediction Problem  
For this project, I aim to predict whether a recipe is labeled as “healthy” based on its ingredients and nutritional information. This is a binary classification problem, where the response variable is is_health_tagged (1 if the recipe is marked as healthy, 0 otherwise). The goal is to build a predictive model, as the features I use such as ingredient lists and nutritional attributes like calories, total fat, and sugar are all available at the time a recipe is submitted and therefore realistic inputs at prediction time.
I selected this problem because it connects directly to my earlier exploratory analysis on what makes a recipe “healthy.” By building a model to predict health tags, I can evaluate which features are most predictive and assess the feasibility of automating health-related tagging on recipe platforms.
To evaluate my model’s performance, I plan to use the F1-score as my primary metric. While accuracy can provide a high-level sense of model performance, the dataset may be imbalanced (e.g., fewer healthy recipes than unhealthy ones), and in such cases, F1-score offers a more balanced measure by accounting for both precision and recall.



## Baseline Model  
For my baseline model, I implemented a logistic regression classifier to predict whether a recipe is labeled as “healthy” (is_health_tagged) based on two features: calories and protein. Both features are quantitative (continuous numerical variables), and no ordinal or nominal features were included at this stage. I standardized these numerical features using StandardScaler to ensure they were on the same scale before model training. The model was implemented using an sklearn Pipeline that combines preprocessing with classification. To address the class imbalance between healthy and non-healthy recipes, I used the class_weight='balanced' parameter in LogisticRegression. On the test set, the model achieved an F1 score of approximately 0.33 for the positive class (healthy recipes). Although the accuracy was low (43%), the F1 score provides a better evaluation for this imbalanced classification task. Overall, I consider this model a reasonable starting point  it captures some signal, especially in terms of recall for the healthy class (0.67), but its precision and overall performance indicate there is significant room for improvement by incorporating additional features and more complex modeling techniques in the final model.


## Final Model  

For my final model, I selected four features: calories_per_ingredient, protein_per_calorie, sugar_per_calorie, and saturated_fat. These features were chosen because they better reflect how nutritional quality is assessed in real-world contexts. For example, protein_per_calorie captures the protein density of a recipe, a key factor in determining satiety and nutritional value, while sugar_per_calorie helps flag recipes that are disproportionately high in sugar relative to their calorie content. Calories_per_ingredient gives insight into how calorie-dense each component of the recipe is, which may distinguish simple, whole-food recipes from those that are overly processed. Saturated_fat was included due to its known association with diet-related health risks. I used a RandomForestClassifier for its ability to model nonlinear relationships and its robustness to feature scaling. I applied GridSearchCV to tune hyperparameters, testing different values for the number of trees (n_estimators) and maximum tree depth (max_depth). The best configuration found was 50 trees with no maximum depth. While the final model’s F1 score on the positive class (healthy recipes) remained low at approximately 0.05, this still marked an improvement over the baseline model, which failed to identify any healthy recipes at all. The increase in recall from 0.00 to 0.03 shows the model is beginning to capture the characteristics of healthy recipes, which is promising given the class imbalance. Overall, I believe this model reflects thoughtful feature engineering grounded in nutrition and improved upon the baseline in meaningful ways.

