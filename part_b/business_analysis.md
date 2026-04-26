# Part B: Business Case Analysis

 ## B1. Problem Formulation

### (a)
This problem can be formulated as a supervised learning regression task where the objective is to predict **items_sold** for each store on a given date. The target variable is items_sold, as it directly measures sales volume and demand.

The input features include transaction_date (to capture seasonality and temporal trends), store_id, store_size, location_type, promotion_type, is_weekend, is_festival, and competition_density. These variables are relevant because they influence customer purchasing behavior and vary across the 50 stores operating in different geographic segments.

Regression is appropriate since the target is continuous. The predictions from this model can be directly used to decide which promotion to deploy in each store, making the solution actionable for business decision-making.

---

### (b)
Items_sold is a more reliable target variable than total revenue because revenue is influenced by pricing strategies, discounts, and product mix, which may distort the true demand signal. For example, a heavy discount might increase units sold but reduce revenue, or higher-priced items might inflate revenue without reflecting increased customer engagement.

By focusing on items_sold, we capture actual customer demand more accurately. This reflects a key principle in machine learning: the target variable should align closely with the business objective and remain stable without being affected by external or confounding factors.

---

### (c)
Using a single global model across all 50 stores may fail to capture regional differences in customer behavior. Stores in urban, semi-urban, and rural areas are likely to respond differently to the same promotion due to differences in demographics, purchasing power, and competition.

A more effective strategy would be to either segment the data and build separate models for different location types or incorporate interaction features (e.g., location_type × promotion_type) within a unified model. This allows the model to learn context-specific relationships and improves its ability to recommend the most effective promotion for each store and scenario.

## B2. Data and EDA Strategy

### (a)
The raw data comes from four tables: transactions, store attributes, promotion details, and a calendar table. These can be joined using common keys such as store_id and transaction_date. Specifically, the transactions table would act as the base, and we would perform left joins with store attributes (on store_id), promotion details (on promotion_type or promotion_id), and the calendar table (on transaction_date).

The grain of the final dataset should be at the **store-date level**, meaning each row represents a specific store on a specific date. This ensures consistency with the target variable (items_sold).

Before modeling, it may be useful to aggregate certain features if required, such as summarizing transaction-level data into daily totals. Additionally, derived features like average sales per store or promotion frequency can help capture broader patterns.

---

### (b)
Before building a model, I would perform exploratory data analysis to understand patterns and relationships in the data.

First, I would analyze the distribution of items_sold using histograms or boxplots to detect skewness and outliers. This helps decide whether transformations are needed.

Second, I would examine sales trends over time using line plots to identify seasonality, trends, and the impact of festivals or weekends. This would guide feature engineering from transaction_date.

Third, I would compare sales across different promotion types and location types using bar charts or grouped averages to understand which promotions perform better in different regions.

Fourth, I would analyze correlations between numerical variables such as competition_density and items_sold to understand their influence on demand.

These analyses help in selecting relevant features, handling outliers, and improving model performance.

---

### (c)
Since around 80% of transactions occur without any promotion, the dataset is imbalanced with respect to promotion_type. This imbalance may cause the model to learn patterns biased toward non-promotional cases, reducing its ability to accurately predict the impact of promotions.

To address this, I would ensure that promotion-related features are properly represented, possibly by giving more attention to promotional cases during analysis. Techniques such as stratified evaluation, resampling, or weighting can also be considered. Additionally, creating features that explicitly capture promotion presence can help the model better distinguish between promotional and non-promotional scenarios.

## B3. Model Evaluation and Deployment

### (a)
Since the data is time-based (monthly store-level data over three years), the train-test split should follow a **temporal split** rather than a random split. The model should be trained on earlier data (e.g., first 80%) and tested on the most recent data (last 20%) to simulate real-world forecasting.

A random split is inappropriate because it would mix past and future data, leading to data leakage and overly optimistic performance.

For evaluation, I would use metrics such as **RMSE (Root Mean Squared Error)** and **MAE (Mean Absolute Error)**. RMSE penalizes larger errors more strongly, while MAE provides a more interpretable average error. In this business context, these metrics help understand how far predictions deviate from actual sales, which directly impacts planning and inventory decisions.

---

### (b)
Feature importance can be used to understand why the model recommends different promotions for the same store across months. For example, in December, factors such as festival periods, higher customer demand, or seasonal trends may increase the effectiveness of Loyalty Points Bonus. In contrast, in March, different conditions such as lower demand or different customer behavior may make Flat Discount more effective.

By analyzing feature importance, we can identify which variables (e.g., month, festival indicator, promotion type) are driving predictions. This helps explain that the model is not inconsistent, but rather adapting to changing conditions. Communicating this to the marketing team builds trust and ensures that recommendations are seen as data-driven rather than arbitrary.

---

### (c)
For deployment, the trained model should be saved (e.g., using joblib or pickle) after training. At the start of each month, new input data (store details, calendar features, and planned promotions) would be prepared and passed into the model to generate predictions and recommendations for all 50 stores.

The process would involve:
- Preparing updated monthly data  
- Feeding it into the saved model  
- Generating predicted sales for different promotion options  
- Selecting the best promotion for each store  

To ensure reliability, monitoring should be implemented by tracking model performance over time (e.g., comparing predicted vs actual sales). If performance degrades due to changing trends or market conditions, the model should be retrained using recent data. This ensures the system remains accurate and relevant.
