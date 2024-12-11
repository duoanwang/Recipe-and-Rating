# Recipe and Rating: Analysis of Calories
Authors: Juntong Ye & Anduo Wang

Overview:

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
For this analysis, we 


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
   - **`n_ingredient`**: Recipes with more ingredients may involve calorie-dense items like butter, sugar, or oils, leading to higher calorie counts.

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

Nominal Features (1):
- **`tags`**: Represents the tags of the recipe (e.g., "dessert," "north-american", "60-minutes-or-less").
  - **Encoding**: Transformed using `OneHotEncoder` to create binary indicators for each tags.

Feature Transformation
The preprocessing steps include:

1. **Standardization**:
   - Applied to quantitative features to ensure they have zero mean and unit variance.

2. **One-Hot Encoding**:
   - Converts nominal categories in the `tags` feature into binary columns, enabling the model to use categorical information.
     
## Model Performance

### Evaluation Metric
- **Root Mean Squared Error (RMSE)**: Used to measure performance.
  - RMSE provides an interpretable error in the same unit as the target variable (calories) and penalizes large errors more heavily.

### Baseline RMSE
- **194.77 calories**


## Final Model

---

## Fairness Analysis


---
