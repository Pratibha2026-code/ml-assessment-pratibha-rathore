### B1 (a) Problem Formulation

Target Variable:
The target variable is the number of items sold (sales volume) per store per month.

Input Features:
- Promotion type (Flat Discount, BOGO, Free Gift, etc.)
- Store location type (urban, semi-urban, rural)
- Store size
- Monthly footfall
- Competition density
- Customer demographics

Type of ML Problem:
This is a supervised learning regression problem.

Justification:
The goal is to predict a continuous numeric value (number of items sold), which makes it a regression problem. The model learns from past data where both inputs and outputs are known.



### B1 (b) Target Variable Choice

Using items sold is more reliable than total revenue because revenue can be influenced by price variations, discounts, and product mix.

For example, a store selling fewer high-priced items may generate higher revenue than a store selling many low-priced items. This can mislead the evaluation of promotion effectiveness.

Items sold directly measures customer response to promotions and reflects actual demand more accurately.

Broader Principle:
This illustrates that the target variable should align closely with the business objective. Choosing the wrong target can lead to misleading conclusions and poor decision-making.



### B1 (c) Modelling Strategy

Instead of using a single global model, a better approach is to use a segmented or hierarchical modelling strategy.

One option is to group stores based on location type (urban, semi-urban, rural) and build separate models for each segment.

Another approach is to use a single model but include store-specific features (such as location type, footfall, and competition) so the model can learn different patterns for different stores.

Justification:
Different stores respond differently to promotions due to variations in customer behaviour and local conditions. A segmented or context-aware model can capture these differences more effectively and improve prediction accuracy.





### B2 (a) Data Joining and Dataset Grain

The four tables (transactions, store attributes, promotion details, and calendar) would be joined using common keys such as store_id and date.

Step 1: Aggregate the transactions table
- Group by store_id and month
- Calculate total items sold (target variable)
- Calculate total revenue (optional feature)

Step 2: Join store attributes
- Merge on store_id to add features like store size, location type, and competition density

Step 3: Join promotion details
- Merge on store_id and date/month to identify which promotion was active

Step 4: Join calendar table
- Merge on date to include features like weekend flag and festival flag

Grain of the dataset:
Each row represents one store for one month (store-month level data).

Aggregations performed:
- Total items sold per store per month
- Total revenue per store per month
- Average daily footfall (if available)
- Count of transaction records per month

This aggregation ensures that the dataset aligns with the business objective of monthly promotion effectiveness.


### B2 (b) Exploratory Data Analysis (EDA)

1. Promotion vs Items Sold (Bar Chart)
- Plot average items sold for each promotion type
- Purpose: Identify which promotions perform best
- Impact: Helps in selecting or encoding promotion features

2. Items Sold Distribution (Histogram)
- Plot distribution of items sold
- Purpose: Check skewness and outliers
- Impact: May require transformation (e.g., log transformation)

3. Store Type vs Sales (Box Plot)
- Compare items sold across urban, semi-urban, and rural stores
- Purpose: Understand variation by location
- Impact: Supports segmentation or feature importance

4. Time-based Trends (Line Chart)
- Plot monthly sales trends
- Purpose: Identify seasonality and festival effects
- Impact: Helps create time-based features

5. Correlation Heatmap
- Check relationships between numeric variables (footfall, size, sales)
- Purpose: Identify strong predictors
- Impact: Helps in feature selection and avoiding multicollinearity




### B2 (c) Handling Data Imbalance

The dataset is imbalanced because 80% of transactions occur without any promotion.

Impact:
The model may become biased towards the majority class (no promotion) and fail to learn the true impact of promotions.

Steps to address this:
- Use stratified sampling to ensure balanced training data
- Apply weighting to give more importance to promotion data
- Perform separate analysis for promotion vs non-promotion cases
- Create a binary feature indicating promotion presence

These steps help the model better understand the effect of promotions.



### B3 (a) Train-Test Split and Evaluation Metrics

Train-Test Split:
Since the data is time-based (monthly over three years), a time-based split should be used.

- Training set: First 2–2.5 years of data
- Test set: Last 6–12 months

A random split is inappropriate because it would mix past and future data, causing data leakage and unrealistic performance evaluation.

Evaluation Metrics:

1. RMSE (Root Mean Squared Error)
- Measures average prediction error with higher penalty for large errors
- Interpretation: Lower RMSE means better prediction accuracy

2. MAE (Mean Absolute Error)
- Measures average absolute difference between predicted and actual values
- Interpretation: Easier to understand in business terms (average units off)

3. R² Score
- Measures how well the model explains variation in sales
- Interpretation: Higher R² indicates better model fit

In this business context, MAE is particularly useful because it directly shows how many items the prediction is off by.



### B3 (b) Feature Importance and Interpretation

Feature importance helps identify which variables influenced the model’s predictions.

To investigate:
- Analyze feature importance scores from the model (e.g., Random Forest feature importance or SHAP values)
- Compare feature values for Store 12 in December vs March

Key factors to examine:
- Seasonal effects (festivals in December)
- Customer behaviour patterns
- Footfall variations
- Promotion effectiveness in different months

Explanation to stakeholders:
The model recommends different promotions because it detects seasonal and behavioural differences. For example, in December (festival season), customers may respond better to loyalty rewards, while in March, price-based discounts may drive higher sales.

Communicating this insight helps the marketing team trust the model and understand that recommendations are context-specific rather than random.



### B3 (c) Model Deployment and Monitoring

Step 1: Save the Model
- Save the trained model using joblib or pickle

Step 2: Prepare New Monthly Data
- Collect new data for each store (store features, promotion options, calendar data)
- Apply the same preprocessing steps used during training

Step 3: Generate Predictions
- Load the saved model
- Input new data and generate predictions for each promotion
- Select the promotion with the highest predicted sales

Step 4: Deployment
- Automate this process to run at the start of each month for all 50 stores

Step 5: Monitoring
- Track model performance using metrics like MAE over time
- Compare predicted vs actual sales
- Monitor data drift (changes in customer behaviour or store patterns)

Step 6: Retraining
- If performance degrades significantly, retrain the model with recent data

This ensures the model remains accurate and relevant over time.