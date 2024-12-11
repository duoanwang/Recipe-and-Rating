# Recipe and Rating: Analysis of Calories
Authors: Juntong Ye & Anduo Wang
---

## Introduction

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
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis
For this analysis, we explored the relationship between the rating and the calories. From this plot, we found that the data points are densely packed around ratings between 3 and 5. Lower ratings (e.g., 1-2) do not show many high-calorie items. Higher ratings are associated with a broader range of calorie counts, including some extremely high-calorie items, as indicated by larger, brighter bubbles. Lower ratings (1-2) are predominantly linked to low-calorie items, with fewer high-calorie outliers.

<iframe
  src="assets/calories vs. rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregates
We are interested in whether recipes with higher calories need more time to prepare, so we investigated their relationship by creating a pivot table and then visualize it. First, We group the recipes by `'minutes'` and calculate the average calories for each preparation time using pd.pivot_table(). We categorize `'minutes'` into meaningful bins (e.g., 0-30 mins, 30-60 mins) to make the analysis clearer. Finally, to visualize the results, we create a bar chart using Plotly to show the average calories for each preparation time category or bin. This helps us understand how calorie counts vary with different preparation times.

<iframe
  src="assets/avg calories by minutes.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
By examine this plot, we noticed that the calories increases as the prepare time increases.

---

## Assessment of Missingness

---

## Hypothesis Testing

- **Null Hypothesis (H₀):** The calorie distributions of desserts and courses are the same.
- **Alternative Hypothesis (Hₐ):** The calorie distributions of desserts and courses differ.

### Test Statistic
The test statistic used is the **Total Variation Distance (TVD)**, which measures the overall difference between two distributions.

### Significance Level
The significance level (α) is set to **0.05**.

### Results
- **Observed TVD:** 0.0000
- **P-value:** 0.000000

### Conclusion
Since the p-value (0.000000) is less than the significance level (0.05), we reject the null hypothesis. This suggests that the calorie distributions of desserts and courses are significantly different.

### Justification
The Total Variation Distance (TVD) is an appropriate test statistic for this hypothesis because it captures the overall differences between two distributions without assuming normality or other specific distributional properties.

### Visualization
The histogram below illustrates the null distribution of permuted TVDs, with a vertical red line indicating the observed TVD:

<iframe
  src="assets/permutation_test_tvd.html"
  width="800"
  height="600"
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

### Reason for Choosing:
Helps quantify calorie prediction for real-world applications like meal planning.

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

Quantitative Features (3):
- **`sugar`**: Represents the sugar content in grams.
- **`total_fat`**: Represents the fat content in grams.
- **`n_steps`**: Represents the number of preparation steps in the recipe.
  - **Encoding**: These features are standardized using `StandardScaler` to ensure all features contribute equally to the regression model.
    
Feature Transformation
The preprocessing steps include:

1. **Standardization**:
   - Applied to quantitative features to ensure they have zero mean and unit variance.

# Model Performance

## Metrics Used

- **Root Mean Squared Error (RMSE)**: Measures the average prediction error in the same units as the target variable (calories). RMSE penalizes larger errors more heavily, making it ideal for this task.
- **Mean Squared Error (MSE)**: Similar to RMSE but in squared units, providing an alternative perspective on error magnitude.
- **R² Score**: Indicates the proportion of variance explained by the model, providing a measure of goodness-of-fit.

## Performance Metrics

- **RMSE**
   - Training set: 202.570722
   - Test set: 195.281338
- **MSE**
  - Training set: 41034.897364
  - Test set: 38134.801087
- **R² Score**
  - Training set: 0.902043 
  - Test set: 0.891276  

---

## Model Performance Analysis

### Strengths:
- **High R² Score**: The \( R^2 \) score of 0.89 on the test set indicates that the model explains 89% of the variance in calorie counts, demonstrating a strong fit.
- **Low Training RMSE**: The training RMSE of 202.57 shows that the model performs well on the training data.

### Weaknesses:
- **Overfitting**: The gap between the training RMSE (202.57) and test RMSE (195.28) suggests that the model is slightly overfitting to the training data, which limits its ability to generalize to unseen data.
- **Large Test RMSE**: The test RMSE of 195.28 means that, on average, the model's predictions deviate by about 195 calories, which might not be ideal for applications requiring high precision.


## Final Model

### Feature Engineering

### Added Features
1. **`sugar_total_fat_interaction`**:
   - Represents the interaction between sugar and total fat content in a recipe.
   - **Reason for Inclusion**: Recipes with both high sugar and high fat often have significantly higher calorie counts. This feature captures non-linear relationships between these two variables.

2. **`nutrition_sum`**:
   - The sum of `sugar`, `protein`, and `carbohydrates`.
   - **Reason for Inclusion**: Macronutrients directly contribute to calorie content, and this feature aggregates their combined impact into a single variable.

---

### Algorithm Chosen
- **Random Forest Regressor**:
  - Random Forests handle non-linear relationships and feature interactions effectively, making them well-suited for predicting calorie counts.

### Hyperparameter Tuning
- **Best Hyperparameters**:
  - `max_depth`: None (no limit on tree depth).
  - `min_samples_leaf`: 2 (ensures each leaf node has at least 2 samples to prevent overfitting).
  - `min_samples_split`: 2 (minimum samples required to split a node).
  - `n_estimators`: 200 (builds 200 decision trees for higher accuracy).

- **Hyperparameter Selection Method**:
  - Used **GridSearchCV** with cross-validation (`cv=3`) to evaluate combinations of hyperparameters.
  - Optimized based on negative Root Mean Squared Error (RMSE).

---
 Final Model Performance vs. Baseline Model

| **Metric**           | **Baseline Model** | **Final Model** |
|-----------------------|--------------------|-----------------|
| **Train RMSE**        | 202.57             | 107.28          |
| **Test RMSE**         | 195.28             | 133.57          |
| **Train R²**          | 0.9020             | 0.9725          |
| **Test R²**           | 0.8913             | 0.9491          |
| **Train MSE**         | 41034.90           | 11509.09        |
| **Test MSE**          | 38134.80           | 17841.06        |

### Improvements
1. **Lower RMSE**: The Final Model achieves significantly lower RMSE for both training and test sets, indicating improved accuracy.
2. **Higher R²**: The Final Model explains 94.91% of the variance in the test data, compared to 89.13% for the Baseline Model.
3. **Reduced Overfitting**: The smaller gap between training and test RMSE demonstrates better generalization to unseen data.

<iframe
  src="assets/feature_importance.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

---

## Fairness Analysis


---
