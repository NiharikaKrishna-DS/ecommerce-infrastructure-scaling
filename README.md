

# Ecommerce Infrastructure Scaling Analysis

This project analyzes ecommerce infrastructure telemetry to understand demand changes, evaluate machine learning readiness, and support proactive scaling decisions during sales events.

## Objectives
- Understand how demand changes over time
- Assess whether the data is suitable for machine learning
- Identify patterns that influence future scaling decisions
- Prepare the dataset for forecasting and proactive infrastructure scaling

## Task 1: Data Understanding and Validation

### Objective
Validate data quality, inspect traffic patterns, and confirm the target variables before building any model.

### Activities
- Dataset inspection:
  - Number of rows and columns
  - Features and data types
  - Time coverage
- Traffic analysis:
  - Active Users
  - Requests Per Minute
  - Orders Per Minute
  - CPU Utilization
  - Memory Utilization
  - Network Throughput
  - Response Time
- Target analysis:
  - Regression target: `next_15min_requests`
  - Classification target: `scale_up_required`
- Missing value assessment
- Outlier review

### Key findings
- Clear traffic variations across sales periods
- Strong positive skew caused by demand spikes during Sale and Flash Sale events
- CPU usage remains consistently high during busy periods
- The scale-up target is moderately imbalanced
- Missing values are limited and manageable
- Extreme demand values represent valid business activity rather than data quality issues

## Task 2: Data Preparation for Machine Learning

### Objective
Transform raw monitoring data into a model-ready format while preserving operational meaning.

### Activities
- Time-based feature engineering:
  - Hour of day
  - Day of week
  - Weekend indicator
- Missing value treatment using median imputation
- Categorical encoding:
  - `sale_event`
  - `region`
  - One-hot encoding
- Outlier assessment using IQR
- Scaling strategy:
  - Applied only where needed, such as linear models
- Target separation before model development

### Key outcomes
The dataset is prepared for:
- time-aware modeling
- handling missing values
- categorical encoding
- outlier-aware preprocessing
- target separation and realistic evaluation

## Technologies Used
- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Scikit-Learn

If you want, I can also make it more polished for GitHub with a short project title and badges.

## Task 3: Feature Engineering and Feature Selection

### Objective
Enhance the raw telemetry data with features that capture operational pressure, remaining capacity, and demand intensity while avoiding target leakage.

### Activities
- Capacity headroom:
  - `current_capacity_rpm - requests_per_min`
  - Lower values indicate higher infrastructure pressure
- Demand intensity:
  - `requests_per_min / active_users`
- Utilization pressure:
  - `(cpu_utilization + memory_utilization + disk_utilization) / 3`
- Resource efficiency:
  - `requests_per_min / cpu_utilization`
- Feature validation:
  - Compare engineered features against `scale_up_required`
  - Review boxplots and feature separation
- Correlation analysis:
  - Assess relationships with `next_15min_requests`
  - Assess relationships with `scale_up_required`
- Dataset separation:
  - Regression features: `X_reg`
  - Classification features: `X_cls`

### Key findings
- Capacity headroom showed strong separation between scale-up and non-scale-up states.
- Utilization pressure and requests per CPU are useful indicators of operational stress.
- Current request volume, active user count, and order volume show strong positive relationships with future demand.
- Scale-up requirements are positively correlated with utilization pressure and negatively correlated with capacity headroom.
- Demand intensity was retained for evaluation but was not the strongest signal by itself.

## Task 4: Demand Forecasting (Regression)

### Objective
Predict the expected request volume for the next 15 minutes using current workload and infrastructure telemetry so that operational teams can act before demand spikes impact performance.

### Activities
- Time-ordered train/test split to preserve real-world event sequencing
- Feature preparation for regression modeling
- Standardization for linear models
- Model comparison:
  - Linear Regression
  - Random Forest Regressor
- Evaluation using:
  - MAE
  - RMSE
  - MAPE
- Actual-vs-predicted trend review across time periods

### Key findings
- Linear Regression achieved the best overall performance for demand forecasting.
- The model maintained a low mean absolute percentage error, approximately 2.2%, indicating strong forecast accuracy for typical traffic conditions.
- The model tracked normal demand patterns reliably and was useful for operational planning.
- Forecast error increased during sale and flash sale periods, where sudden traffic surges were harder to predict.
- This means the forecast is highly valuable as a decision-support tool, especially when paired with operational safeguards during high-traffic promotional windows.

### Business value
The regression model provides early visibility into upcoming demand, helping the infrastructure team anticipate resource pressure, plan capacity expansion, and reduce the risk of reactive scaling during critical sales events.

## Task 5: Proactive Scale-Up Classification

### Objective
Determine whether proactive infrastructure scaling should be triggered based on the current operational state.

### Activities
- Separate classification features from the regression target to avoid leakage
- Train and compare:
  - Decision Tree Classifier
  - Random Forest Classifier
- Evaluate using:
  - Accuracy
  - Precision
  - Recall
  - F1-score
  - Confusion matrix
- Assess operational trade-offs between missing a required scale-up and triggering unnecessary scaling

### Key findings
- Random Forest was selected as the preferred model for proactive scaling decisions.
- It achieved:
  - Accuracy: 96.03%
  - Precision: 97.99%
  - Recall: 96.70%
  - F1-score: 97.34%
- The Decision Tree model performed well but showed slightly weaker recall and a higher number of false negatives.
- The Random Forest model reduced both unnecessary scaling actions and missed scale-up events, making it the strongest balance between reliability and cost.
- Since false negatives are more operationally costly than false positives, recall was treated as especially important.

### Recommendation
For NimbusCart, the Random Forest classifier is the recommended model for proactive scaling decisions because it delivers stronger predictive reliability while reducing the risk of infrastructure saturation during demand spikes.

### Output artifacts
- Confusion matrix for Decision Tree: `outputs/tasks5/Confusion_matrix_DT.png`
- Confusion matrix for Random Forest: `outputs/tasks5/Confusion_matrix_RF.png`

## Summary
This analysis shows that the system can support both forecasting and proactive actioning:
- The regression model estimates future request volume from current conditions.
- The classification model decides when scaling should be triggered before service degradation occurs.
- Together, these outputs provide a practical basis for infrastructure planning, risk reduction, and operational decision support during high-demand periods.
