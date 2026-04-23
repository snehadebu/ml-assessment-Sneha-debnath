# Part B: Business Case Analysis

## B1. Problem Formulation

### (a) Machine Learning Problem

The objective is to predict the optimal promotion strategy for each store in order to maximise the number of items sold.

- **Target Variable:** items_sold (continuous numerical variable)
- **Input Features:**
  - Store attributes: store_size, location_type
  - Customer behaviour: footfall, historical sales
  - Promotion type: Flat Discount, BOGO, Free Gift, Category Offer, Loyalty Points
  - Temporal features: month, seasonality, festivals, weekends
  - Competition: competition_density

- **Problem Type:** Supervised Learning (Regression or Classification)

This can be formulated as:
- A **regression problem** if predicting expected items sold for each promotion
- Or a **classification problem** if directly predicting the best promotion category

Regression is often preferred because it allows comparison across all promotion options and selection of the one that maximises predicted sales.

---

### (b) Why Use Items Sold Instead of Revenue

Using total revenue as a target can be misleading because promotions directly affect pricing.

For example:
- A heavy discount may reduce revenue per item but increase sales volume
- A premium pricing strategy may increase revenue but reduce number of items sold

Therefore, **items_sold is a more reliable metric** because:
- It directly measures customer response to promotions
- It is not distorted by pricing strategies
- It reflects demand more accurately

**Broader Principle:**
The target variable in ML should align with the **true business objective** and should not be influenced by external factors that distort the relationship being modelled.

---

### (c) Alternative Modelling Strategy

Instead of using a single global model, a better approach is:

**Segmented or Hierarchical Modelling**

- Build separate models for:
  - Urban, semi-urban, and rural stores
  OR
- Include store-level features and interactions in a global model

**Justification:**
- Customer behaviour differs significantly by location
- Promotions may perform differently depending on demographics and competition
- A segmented approach captures these variations more effectively

This leads to more accurate and personalised recommendations.

---

## B2. Data and EDA Strategy

### (a) Data Joining and Dataset Design

The raw data consists of:
- Transactions table
- Store attributes table
- Promotion details table
- Calendar table

**Joining Strategy:**
- Join transactions with store attributes using store_id
- Join with promotion data using promotion_id or date
- Join with calendar using transaction_date

**Final Dataset Grain:**
- One row per **store per month**

**Aggregations:**
- Total items_sold per store per month
- Average basket size
- Monthly footfall
- Promotion applied in that month
- Competition and store features

This aggregated dataset is suitable for modelling store-level monthly performance.

---

### (b) Exploratory Data Analysis (EDA)

1. **Promotion vs Sales Plot**
   - Compare average items_sold across promotions
   - Helps identify which promotions perform better

2. **Time Series Analysis**
   - Plot sales over time
   - Detect seasonality, trends, and festival spikes

3. **Correlation Heatmap**
   - Identify relationships between features
   - Helps in feature selection

4. **Distribution Plots (Histograms/Boxplots)**
   - Check spread of sales, footfall, basket size
   - Detect outliers

**Impact on Modelling:**
- Helps create new features (seasonality, lag features)
- Identifies important predictors
- Guides transformation and scaling decisions

---

### (c) Handling Promotion Imbalance

Since 80% of transactions occur without promotions:

**Impact:**
- Model may become biased toward predicting "no promotion"
- Poor learning of promotion effects

**Solutions:**
- Resampling techniques (oversample minority cases)
- Use class weighting
- Ensure balanced training data
- Evaluate model separately on promotion cases

---

## B3. Model Evaluation and Deployment

### (a) Train-Test Split and Metrics

**Train-Test Split:**
- Use time-based split:
  - Train: First ~2.5 years
  - Test: Last ~6 months

**Why not random split:**
- Breaks time order
- Causes data leakage
- Unrealistic evaluation

**Evaluation Metrics:**

- **MAE (Mean Absolute Error):**
  Measures average prediction error in units of items sold

- **RMSE (Root Mean Squared Error):**
  Penalises large errors more heavily

- **MAPE (Mean Absolute Percentage Error):**
  Shows error in percentage terms

- **Business Metric (Sales Uplift):**
  Measures real-world effectiveness of recommendations

---

### (b) Explaining Different Promotion Recommendations

The model may recommend:
- Loyalty Points Bonus in December
- Flat Discount in March

**Using Feature Importance:**
- Identify key drivers such as:
  - Seasonality (month)
  - Customer demand patterns
  - Past sales trends

**Interpretation:**
- December → High demand season → loyalty rewards retain customers
- March → Lower demand → discounts attract more buyers

**Communication to Marketing Team:**
Explain in simple terms:
- “In high-demand months, retaining customers is more effective”
- “In low-demand months, price incentives drive sales”

---

### (c) Deployment and Monitoring

**1. Model Saving:**
- Save model using joblib or pickle

**2. Monthly Data Pipeline:**
- Collect latest store data
- Apply same preprocessing steps
- Generate features (month, trends, etc.)

**3. Prediction:**
- Load model
- Generate promotion recommendations for all stores

**4. Automation:**
- Schedule monthly execution (cron/Airflow)

**5. Monitoring:**

- Track prediction errors (MAE, RMSE)
- Monitor business KPIs (sales uplift)
- Detect data drift (changes in input patterns)

**Retraining Trigger:**
- Significant drop in performance
- Change in customer behaviour
- Seasonal shifts

**Version Control:**
- Maintain model versions for rollback if needed

---

## Conclusion

This approach ensures:
- Accurate promotion recommendations
- Scalable deployment across all stores
- Continuous monitoring and improvement of model performance
