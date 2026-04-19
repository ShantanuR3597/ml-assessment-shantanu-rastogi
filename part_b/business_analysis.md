## B1(a) Problem Formulation

This can be formulated as a supervised machine learning regression problem.

### Target Variable
The target variable is:

- items_sold

The objective is to predict how many items a store is likely to sell under a given promotion in a given month.

### Candidate Input Features
Possible input features include:

- Promotion type
- Store size
- Store location (urban, semi-urban, rural)
- Monthly footfall
- Local competition density
- Customer demographics
- Calendar variables (month, weekend, festivals)
- Historical sales patterns
- Store-specific behavior
- Seasonal factors

### ML Problem Type
This is a supervised regression problem because:

- Historical labeled data exists (features + observed items sold)
- The target variable (items_sold) is continuous numeric data
- The goal is to predict a quantity, not classify a category

Regression is appropriate because the business wants to maximize sales volume by selecting the best promotion based on predicted outcomes.

## B1(b)

Using items_sold is more reliable than using total sales revenue because revenue can be influenced by factors beyond promotion effectiveness, such as pricing changes, discounts, product mix, inflation, or premium products being sold.

For example, a promotion may increase the number of units sold significantly, but revenue may still appear lower if the promotion involves discounted prices. In that case, revenue could give a misleading picture of promotion performance.

Items sold is a more direct measure of the business objective in this problem, which is to maximize sales volume through better promotion decisions.

This illustrates a broader principle in real-world machine learning: the target variable should align closely with the actual business objective and should measure the outcome we truly want the model to optimize. Choosing the wrong target can lead to misleading predictions, even if the model performs well technically.

## B1(c)

Instead of using a single global model across all 50 stores, I would use a segmented or hierarchical modeling strategy.

One approach would be to group stores by similar characteristics, such as:

- Urban vs semi-urban vs rural
- Store size
- Customer demographics
- Footfall patterns

Then separate models could be trained for each segment, allowing the model to capture differences in how promotions perform across store types.

Another strong approach would be a hierarchical (multi-level) model or a global model with store-level features and store-specific effects, so the model can learn both overall patterns and store-level differences.

This is preferable to a single global model because the same promotion may perform differently in different store contexts. A segmented or hierarchical strategy improves prediction accuracy and produces recommendations that are better tailored to each store.


## B2(a)

I would join the four tables using common keys:

- Transactions table joined with store attributes using store_id
- Transactions joined with promotion details using promotion_id or promotion type
- Transactions joined with calendar data using transaction_date

This would create a single modeling dataset combining sales, store characteristics, promotions, and time-based factors.

### Final Modeling Grain

The final modeling dataset should have:

- One row = one store for one month under one promotion

This grain aligns directly with the business problem, since the goal is to recommend the best promotion for each store each month.

### Aggregations Before Modeling

Before modeling, I would aggregate or derive features such as:

- Total items sold per store-month
- Monthly footfall averages
- Promotion-level performance history
- Average basket size
- Historical sales trends (lag features)
- Number of festival or weekend days in the month
- Competition density indicators
- Store-level historical response to promotions

These aggregations help convert raw transactional data into features more suitable for prediction and decision-making.

## B2(b)

Before modeling, I would perform the following exploratory analyses:

### 1. Promotion Type vs Items Sold
A comparison of average items sold across different promotion types.

**Visualization:**
- Boxplot or bar chart

**Purpose:**
This helps identify whether some promotions consistently outperform others and whether promotion effectiveness varies significantly.

---

### 2. Sales Trends Over Time (Seasonality Analysis)
Analyze monthly sales trends and possible seasonal patterns.

**Visualization:**
- Time-series line plot

**Purpose:**
This helps identify recurring seasonal effects, demand cycles, and time-based patterns that may influence model features.

---

### 3. Store-Level Performance Comparison
Compare items sold across store types, store sizes, or locations.

**Visualization:**
- Grouped bar chart or boxplot

**Purpose:**
This helps determine whether different store segments respond differently to promotions.

---

### 4. Correlation Analysis
Analyze relationships among numerical variables.

**Visualization:**
- Correlation heatmap

**Purpose:**
This helps identify important predictors, possible feature relationships, and multicollinearity concerns.

---

### How This Informs Modeling

These analyses help:

- Identify useful predictive features
- Detect segmentation opportunities
- Guide feature engineering decisions
- Reveal seasonality effects
- Improve model design by understanding drivers of promotion performance

## B2(c)

This imbalance could be problematic because the model may become biased toward learning patterns from non-promotion periods, simply because they dominate the data.

As a result:

- The model may under-estimate the effect of promotions
- Promotion-related patterns may be poorly learned
- Predictions for promotion performance may become less reliable

### Ways to Handle This

### 1. Resampling Techniques
Use oversampling for promotion cases or undersampling for non-promotion cases to create a more balanced training set.

---

### 2. Weighted Modeling
Assign greater importance or weights to promotion-related observations during model training so the model pays more attention to them.

---

### 3. Separate or Segmented Analysis
Analyze promotion and non-promotion periods separately, or build specialized models focused on promotion response.

---

### 4. Feature Engineering
Create strong promotion-related features, such as:

- Promotion history
- Promotion frequency
- Store-specific promotion response

This helps the model better capture promotion effects despite imbalance.

## B3(a)

I would use a time-based train-test split rather than a random split.

For example:

- Use earlier months as training data
- Use the most recent months as test data

This is appropriate because the data is time-ordered and the model is meant to predict future promotion performance.

### Why Random Split is Problematic

A random split could cause data leakage by allowing future information to influence training.

This would create unrealistically optimistic performance estimates and would not reflect real-world forecasting conditions.

### Evaluation Metrics

I would evaluate the model using:

- RMSE (Root Mean Squared Error)  
Measures average prediction error while penalizing larger errors more heavily.

- MAE (Mean Absolute Error)  
Measures average absolute prediction error and is easy to interpret.

These metrics are appropriate because the target variable (items_sold) is continuous and the problem is a regression task.

## B3(b)

This can happen because promotion recommendations may depend not only on the store itself, but also on changing contextual factors such as:

- Seasonality
- Festival periods
- Competitive conditions
- Customer demand patterns
- Historical promotion performance
- Changes in footfall or local behavior

As a result, the same store may receive different recommended promotions in different months because the surrounding conditions have changed.

### Explaining the Recommendation

I would use feature importance analysis or SHAP (SHapley Additive Explanations) to explain the model’s recommendation.

These methods can show which factors influenced the prediction most, for example:

- A festival period increasing demand
- High competition favoring a discount promotion
- Seasonal patterns favoring a bundle offer

This makes the recommendation interpretable and helps stakeholders understand why a different promotion was selected.

## B3(c)

I would deploy the model as part of a monthly prediction pipeline.

### Deployment Workflow

### 1. Save the Trained Model
The trained model and preprocessing pipeline would be saved using tools such as:

- joblib
- pickle

This allows the same model to be reused for future predictions.

---

### 2. Monthly Prediction Process
At the start of each month:

- New store and promotion data is collected
- The same preprocessing steps are applied
- The model predicts expected items sold for possible promotions
- The best-performing promotion is recommended for each store

This could be integrated into a dashboard or decision-support system for business teams.

---

### 3. Monitoring Model Performance
After deployment, I would monitor:

- Prediction errors (RMSE, MAE)
- Promotion recommendation outcomes
- Data drift (changes in customer or store behavior)
- Model performance degradation over time

Monitoring ensures the model remains reliable.

---

### 4. Periodic Retraining
The model should be retrained periodically using new data, for example:

- Monthly
- Quarterly
- Or whenever significant drift is detected

Retraining allows the model to adapt to changing business conditions and maintain performance.
