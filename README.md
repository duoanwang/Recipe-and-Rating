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

## Framing a Prediction Problem

### Prediction
Predict the Calories of a Recipe

### Type
Regression

### Response Variable
Calorie Count (Numerical)


### Features:
- **nutrition('total_fat', 'sugar', 'sodium', 'protein', 'saturated_fat', 'carbohydrates')**
- **tags**
- **n_step**
- **n_ingradient**

### Reason for Choosing:
Helps quantify calorie prediction for real-world applications like meal planning.

### Metric
Mean Absolute Error (MAE) or Mean Squared Error (MSE).





## Baseline Model

---

## Final Model

---

## Fairness Analysis


---
