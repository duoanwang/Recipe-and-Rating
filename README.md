# Recipe and Rating: Analysis of Calories
Authors: Juntong Ye & Anduo Wang

---

## Introduction
Having knowledge about the relationship between recipe attributes and ratings offers important insights into how nutrition and food preferences relate. In order to investigate the calorie content of recipes, identify the critical factors influencing calories, and investigate if calorie count affects user ratings, we analyze a dataset of recipes and ratings. In addition to being an intriguing investigation of the relationship between flavor and nutrition, this study offers readers useful advice on how to strike a balance between enjoyment and health when making cooking decisions. This investigation clarifies how ingredients, preparation methods, and user preferences influence the meals we develop, eat, and enjoy, regardless of your interests as a foodie or a health-conscious person.

The two dataset consisting of recipes and ratings posted since 2008 on [food.com](https://www.food.com/). The original purpose of the datasets is for the recommender system research paper, Generating Personalized Recipes from Historical User Preferences by Majumder et al.

The first dataset, recipe, contains 83782 rows, indicating 83782 unique recipes, with 10 columns recording the following information:

| Column           | Description                                                                 |
|:-----------------|:----------------------------------------------------------------------------|
| 'name'           | Recipe name                                                                 |
| 'id'             | Recipe ID                                                                   |
| 'minutes'        | Minutes to prepare recipe                                                   |
| 'contributor_id' | User ID who submitted this recipe                                           |
| 'submitted'      | Date recipe was submitted                                                   |
| 'tags'           | Food.com tags for recipe                                                    |
| 'nutrition'      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value”                      |
| 'n_steps'        | Number of steps in recipe                                                   |
| 'steps'          | Text for recipe steps, in order                                             |
| 'description'    | User-provided description                                                   |
| 'ingredients'    | Text for recipe ingredients                                                 |
| 'n_ingredients'  | Number of ingredients in recipe                                             |

Here is the other dataset, interaction, which contains 731927 rows with a review and rating from the user on a specific recipe. The columns it includes are:

| Column     | Description                          |
|:-----------|:-------------------------------------|
| 'user_id'  | User ID                              |
| 'recipe_id'| Recipe ID                            |
| 'date'     | Date of interaction                  |
| 'rating'   | Rating given                         |
| 'review'   | Review text                          |

By combining these 2 dataset and perform analysis, we are able to understand the elements that influence a recipe's caloric content and its relationship to user ratings, we can uncover patterns in how people perceive and enjoy food. Do meals with more calories tend to be more popular, or are nutritional factors important in their attract? This analysis gives people the ability to make educated dietary decisions, helps recipe developers create recipes that suit a variety of tastes, and offers insights into customer behavior. In the end, investigating these relationships improves our comprehension of how innovative cooking fits in with health-conscious trends.

---

## Data Cleaning and Exploratory Data Analysis

In order to conduct a more efficient and accurate analysis, we first cleaned the data in the following steps:

1. Left merge the recipes and interactions datasets together.
   - This step helps match the unique recipes with their ratings and reviews.
2. In the merged dataset, fill all ratings of 0 with np.nan.
   - By filling out the ratings with 0 as values, we avoided the missing values in the dataset. Since the rating scales on the food.com is from 1 to 5, a value of 0 means that the data is missing and should be considered as NaN values to ensure proper handling during computations. Many statistical or machine learning tools automatically ignore np.NaN values during calculations (e.g., mean, median). Keeping 0 instead of np.NaN could artificially lower averages or skew other statistics. Also, it's straightforward to filter out missing ratings or apply data imputation techniques without affecting valid data.
3. Find the average rating per recipe and add it back
   - We then calculated the average rating for each recipe to get a more comprehensive summary of their rating since there are multiple ratings from different raters in the dataset. 
4. split the strings in 'nutrition' column into 7 different columns containing the `'calories'`, `'total_fat'`, `'sugar'`, `'sodium'`, `'protein'`, `'saturated_fat'`, and `'carbohydrates'` of each recipe.
   - We splited this column because we are interested in the calories of the recipes which was included in the original 'nutrition' column. We first strip '[]', split the ', ', and then apply a lambda function to store the elements in a list. We then created a dataframe with these values and add them back into our dataframe.

These four steps helped us created the cleaned dataset that we will be using the in later exploration and analysis.

### Resulting Dataframe
Here are the columns of the final dataframe.

| Column                  | Description    |
| :---------------------- | :------------- |
| `'name'`                | object         |
| `'id'`                  | int64          |
| `'minutes'`             | int64          |
| `'contributor_id'`      | int64          |
| `'submitted'`           | datetime64[ns] |
| `'tags'`                | object         |
| `'nutrition'`           | object         |
| `'n_steps'`             | int64          |
| `'steps'`               | object         |
| `'description'`         | object         |
| `'ingredients'`         | object         |
| `'n_ingredients'`       | int64          |
| `'rating'`              | float64        |
| `'calories (#)'`        | float64        |
| `'total fat (PDV)'`     | float64        |
| `sugar (PDV)'`          | float64        |
| `'sodium (PDV)'`        | float64        |
| `'protein (PDV)'`       | float64        |
| `'saturated fat (PDV)'` | float64        |
| `'carbohydrates (PDV)'` | float64        |

### Univariable Analysis
For this analysis, we examine the distribution of the calories in a recipe. We can see from the boxplot that it is heavily skewed due to extreme outliers. The majority of the data points are concentrated near zero or within a relatively low range of calorie values(lower than 1000). The large difference between the upper fence and the maximum value highlights the outliers which extending up to 45,609 calories.

<iframe
  src="assets/calories dist.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>

### Bivariate Analysis
For this analysis, we explored the relationship between the rating and the calories. From this plot, we found that the data points are densely packed around ratings between 3 and 5. Lower ratings (e.g., 1-2) do not show many high-calorie items. Higher ratings are associated with a broader range of calorie counts, including some extremely high-calorie items, as indicated by larger, brighter bubbles. Lower ratings (1-2) are predominantly linked to low-calorie items, with fewer high-calorie outliers.

<iframe
  src="assets/calories vs. rating.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>

### Interesting Aggregates
We are interested in whether recipes with higher calories need more time to prepare, so we investigated their relationship by creating a pivot table and then visualize it. First, We group the recipes by `'minutes'` and calculate the average calories for each preparation time using pd.pivot_table(). We categorize `'minutes'` into meaningful bins (e.g., 0-30 mins, 30-60 mins) to make the analysis clearer. Finally, to visualize the results, we create a bar chart using Plotly to show the average calories for each preparation time category or bin. This helps us understand how calorie counts vary with different preparation times.

Here is the pivot table for preparation time of recipes and their calories:

| prep_time_category   |   calories |
|:---------------------|:-----------|
| 0-30 mins            |    347.861 |
| 30-60 mins           |    445.806 |
| 1-2 hours            |    558.003 |
| 2-5 hours            |    565.975 |
| 5+ hours             |    582.011 |

<iframe
  src="assets/avg calories by minutes.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>
By examine this plot, we noticed that the calories increases as the prepare time increases.

---

## Assessment of Missingness
We found that there are 3 columns contain missing values, which are `'name'`, `'description'`, and `'rating'`. 

### NMAR Analysis
We assess the data and believe that the `'description'` column is NMAR. It represents the description of the recipe that the person who uploaded the recipe write about. If users feel that the recipe is straightforward, they may think that a description is unnecessary or redundant, especially for commonly known dishes. Also, users might be hesitant to include a description if they feel it reveals personal stories, preferences, or other details they are not comfortable sharing publicly. 

### Missingness Dependency
We wanted to examine the missingness of 'rating' in the merged DataFrame by testing the dependency of its missingness. We are interested in whether the missiness in the 'rating' column depends on the column 'n_steps', which is the number of steps take to make the dish, or the column 'Sodium', which is the amount of sodium in the recipe.

1. N_steps and Rating

   **Null Hypothesis:** The missingness of ratings does not depend on the number of steps it takes to make the dish.
   
   **Alternate Hypothesis:** The missingness of ratings does depend on the number of steps it takes to make the dish.
   
   **Test Statistic:** The difference in means between the group of recipes with missing ratings and without missing ratings.
   
   **Significance Level:** 0.05
   
   We ran a permutation test by shuffling the missingness of rating for 1000 times to collect 1000 simulating mean differences in the two         distributions as described in the test statistic. Here is the Empirical Distribution plot of the Mean Difference in N_steps:
<iframe
  src="assets/Empirical Distribution of the Mean Difference in N_steps.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>
   
The observed statistic we calculated is approximately 1.5, which is indicated in this plot by the red vertical line. We can also see that      there is no simulated test statistics larger than that, which means that the observed result is more extreme than what would be expected by    random chance under the null hypothesis.

<iframe
  src="assets/KDE Plot of N_steps.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>

This KDE plot shows two difference peaks for the two groups, which also suggests a dependency between the missingness of 'rating' and 'n_steps'.

By calculating the P-values, we get a result of 0 which < 0.05, which means that we reject the null hypothesis that the missingness of      'rating' does not depend on the number of steps it takes to make the dish. This indicates that the missingness of 'rating' does depend on  'n_steps'

2. Sodium and Rating

   **Null Hypothesis:** The missingness of ratings does not depend on the amount of Sodium in the recipe.
   
   **Alternate Hypothesis:** The missingness of ratings does depend on the amount of Sodium in the recipe.
   
   **Test Statistic:** The difference in means between the group of recipes with missing ratings and without missing ratings.
   
   **Significance Level:** 0.05

   We performed a permutation test by shuffling the missingness of rating for 1000 times to collect 1000 simulating mean differences in the       two distributions as described in the test statistic.
<iframe
  src="assets/Empirical Distribution of the Mean Difference in Sodium.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>
The observed statistic we calculated is approximately -0.34, which is indicated in this plot by the red vertical line. We can also see that this line is in the middle of the distribution, indicating that this test statistic is not extreme compared to the distribution of test statistics generated by random permutations.

<iframe
  src="assets/KDE Plot of Sodium.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>

This KDE plot shows a very overlapped curve between the two groups, which suggest that the missingness of rating is not strongly related to the sodium amount.

By calculating the P-values, we get a result of 0.893 which > 0.05, which means that we fail to reject the null hypothesis that the missingness of 'rating' does not depend on the sodium amount. This furthermore supports that the missingness of 'rating' indeed does not depend on 'sodium'.

---

## Hypothesis Testing

- **Null Hypothesis (H₀):** The calorie distributions of recipes with sugar and without sugar are the same.
- **Alternative Hypothesis (Hₐ):** he calorie distributions of recipes with sugar and without sugar differ.

### Test Statistic
The test statistic used is the **Total Variation Distance (TVD)**, which measures the overall difference between two distributions.

### Significance Level
The significance level (α) is set to **0.05**.

### Results
- **Observed TVD:** 4.0799106421311744e-07
- **P-value:** 0.084500

### Conclusion
Since the **p-value (0.084500)** is greater than the significance level of 0.05, we fail to reject the null hypothesis. This suggests that there is insufficient evidence to conclude that the calorie distributions of recipes with sugar and without sugar are different.


### Justification
The Total Variation Distance (TVD) is an appropriate test statistic for this hypothesis because it captures the overall differences between two distributions without assuming normality or other specific distributional properties.

### Visualization
The histogram below illustrates the null distribution of permuted TVDs, with a vertical red line indicating the observed TVD:

<iframe
  src="assets/sugar_vs_no_sugar_tvd.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>

---
## Framing a Prediction Problem

### Prediction
Predict the calorie count of a recipe based on its nutritional and preparation-related features.

### Type
Regression

### Response Variable
Calorie Count (Numerical)


### Features
We use features that are both descriptive and predictive of calorie count:

1. **Nutritional Information**:
   - **`total_fat`**: Recipes with higher fat content typically have higher calorie counts, as fat is calorie-dense (9 calories per gram).
   - **`sugar`**: Recipes with high sugar content, like desserts, often have higher calorie counts due to added sugars.
   - **`sodium`**: Although sodium itself has no calories, it may correlate with recipe types
   - **`protein`**: Protein contributes to calorie count (4 calories per gram) and is often higher in main courses.
   - **`saturated_fat`**: A type of fat that contributes heavily to calorie count and can distinguish between healthy and indulgent recipes.
   - **`carbohydrates`**: Carbs are a major calorie source (4 calories per gram), particularly in baked goods, desserts, and grains.

2. **Preparation Details**:
   - **`n_step`**: The number of preparation steps can indicate recipe complexity, indirectly reflecting calorie level.
   
3. **Categorical**:
   - **`desserts`**: A binary feature indicating whether a recipe falls into the "desserts" category (1) or not (0).
   - **`hour_cook`**: A binary feature indicating whether a recipe falls into the "hour_cook" category (1) or not (0) represent 60 minutes and less cooking time.

### Reason for Choosing:

The selected features were chosen because they directly correlate with calorie count by macronutrient information and cooking attributes that influence calorie variations in recipes. This model has practical applications in real-world scenarios such as personalized meal planning, dietary tracking, or recipe modification for health-conscious individuals. The diversity of features, combining nutritional details with contextual preparation information (e.g., **`n_step`**）, allows the model to take a holistic approach to calorie prediction. Additionally, binary categories such as **`desserts`** and **`hour_cook`** provide clear distinctions, enabling the model to better capture the underlying patterns in different recipe types.
### Metric
RSME

### WHY RMSE?
1. **Definition**:
   RMSE is defined as:
   ```
   RMSE = sqrt(1/n * sum((y_i - y_hat_i)^2))
   ```
   where `y_i` is the true calorie count, and `y_hat_i` is the predicted calorie count.

2. **Interpretability**: RMSE is expressed in the same units as the response variable (calories), making it easy to understand.

3. **Penalty for Larger Errors**: RMSE penalizes large errors more heavily than MAE, making it ideal for calorie predictions where overfitting or underfitting can significantly impact user decisions.


## Baseline Model

---
The baselin model is a **Linear Regression** model, implemented using a scikit-learn pipeline that combines preprocessing and regression.

### Pipeline Components:
- **Preprocessing**: Handles feature transformations, including standardization of numerical features and one-hot encoding of categorical features.
- **Regressor**: Uses a simple linear regression model to predict the calorie count.

### Features in the Model
The model uses the following features to predict calorie count:

### Quantitative Features (3):
- **`sugar`**: Represents the sugar content in grams.
- **`total_fat`**: Represents the fat content in grams.
- **`n_steps`**: Represents the number of preparation steps in the recipe.

### Categorical Feature (1):
- **`desserts`**: A binary feature indicating whether a recipe falls into the "desserts" category (1) or not (0).

Feature Transformation
The preprocessing steps include:

1. **Standardization**:
   - Applied to quantitative features to ensure they have zero mean and unit variance.
2. **One-Hot Encoding**:
   - Applied to the categorical feature to convert it into a binary format suitable for regression.

# Model Performance

## Metrics Used

- **Root Mean Squared Error (RMSE)**: Measures the average prediction error in the same units as the target variable (calories). RMSE penalizes larger errors more heavily, making it ideal for this task.
- **Mean Squared Error (MSE)**: Similar to RMSE but in squared units, providing an alternative perspective on error magnitude.
- **R² Score**: Indicates the proportion of variance explained by the model, providing a measure of goodness-of-fit.

## Performance Metrics

- **RMSE**
   - Training set: 198.726596
   - Test set: 191.995719
- **MSE**
  - Training set: 39492.259841
  - Test set: 36862.356260
- **R² Score**
  - Training set: 0.905725
  - Test set: 0.894903

---

## Model Performance Analysis

## Strengths
- **High R² Score**: The R² score of 0.89 on the test set indicates that the model explains 89% of the variance in calorie counts, demonstrating a strong fit.
- **Low Prediction Error**: The RMSE values for both the training and test sets are close, suggesting that the model's predictions are consistent and accurate.

## Weaknesses
- **Overfitting**: The slightly higher R² score on the training set compared to the test set (0.905 vs. 0.894) suggests a minor overfitting issue.
- **Prediction Magnitude**: The RMSE of 191.99 on the test set indicates that the model’s predictions deviate by nearly 192 calories on average, which may not be precise enough for applications demanding high accuracy.



## Final Model

### Feature Engineering

### Added Features
1. **`sugar_total_fat_interaction`**:
   - Represents the interaction between sugar and total fat content in a recipe.
   - **Reason for Inclusion**: Recipes with both high sugar and high fat often have significantly higher calorie counts. This feature captures non-linear relationships between these two variables.

2. **`nutrition_sum`**:
   - The sum of `sugar`, `protein`, and `carbohydrates`.
   - **Reason for Inclusion**: Macronutrients directly contribute to calorie content, and this feature aggregates their combined impact into a single variable.

3. **`hour_cook`**:
   - **Reason for Inclusion**: Cooking time under 60 minutes to see if impact of calories.

---

### Algorithm Chosen
- **Random Forest Regressor**:
  - Random Forests handle non-linear relationships and feature interactions effectively, making them well-suited for predicting calorie counts.

### Hyperparameter Tuning
- **Best Hyperparameters**:
  - `max_depth`: 10.
  - `min_samples_leaf`: 2 (ensures each leaf node has at least 2 samples to prevent overfitting).
  - `min_samples_split`: 2 (minimum samples required to split a node).
  - `n_estimators`: 50 (builds 50 decision trees for higher accuracy).

- **Hyperparameter Selection Method**:
  - Used **GridSearchCV** with cross-validation (`cv=3`) to evaluate combinations of hyperparameters.
  - Optimized based on negative Root Mean Squared Error (RMSE).

---
### Final Model Performance vs. Baseline Model

| **Metric**           | **Baseline Model** | **Final Model** |
|-----------------------|--------------------|-----------------|
| **Train RMSE**        | 198.73             | 138.51          |
| **Test RMSE**         | 191.99             | 132.30          |
| **Train R²**          | 0.9057             | 0.9542          |
| **Test R²**           | 0.8949             | 0.9501          |
| **Train MSE**         | 39492.26           | 19185.57        |
| **Test MSE**          | 36862.36           | 17503.28        |

### Improvements
1. **Lower RMSE**: The Final Model achieves significantly lower RMSE for both training and test sets, indicating improved accuracy.
2. **Higher R²**: The Final Model explains 95.01% of the variance in the test data, compared to 89.49% for the Baseline Model.
3. **Reduced Overfitting**: The smaller gap between training and test RMSE demonstrates better generalization to unseen data.

<iframe
  src="assets/feature_importance_final.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>

---

## Fairness Analysis

### Group Definitions
- **Group X (Desserts)**: Recipes classified as desserts (`desserts = 1`).
- **Group Y (Non-Desserts)**: Recipes not classified as desserts (`desserts = 0`).

### Evaluation Metric
- **Root Mean Squared Error (RMSE)**: Evaluates the average prediction error, penalizing larger errors more heavily.

### Hypotheses
- **Null Hypothesis H₀**: The model performs equally well for desserts (Group X) and non-desserts (Group Y).
- **Alternative Hypothesis Hₐ**: The model performs differently for desserts and non-desserts.

### Test Statistic
- Absolute difference in RMSE between desserts and non-desserts groups.

### Significance Level
- α = 0.05: The standard threshold for rejecting the null hypothesis.

### Results
- **Observed RMSE Difference**: 51.8725
- **P-value**: 1.0000
  
<iframe
  src="assets/fairness_test_desserts.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>

### Conclusion
- Since the **p-value (1)** is greater than the significance level α, we **fail to reject the null hypothesis**.
- This indicates that there is insufficient evidence to suggest the model performs fair for dessert and non-dessert recipes. The observed difference in RMSE is likely due to random chance.

---
