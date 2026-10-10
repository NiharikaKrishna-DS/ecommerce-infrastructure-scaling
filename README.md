Online shopping platforms experience constantly changing demand patterns. During normal operations the traffic may be predictable, but during Sale and Flash Sale events the infrastructure can experience sudden spikes in users, requests, and resource utilization.
The goal of this is to analyze infrastructure telemetry data and understand:
How demand changes over time
Whether the data is suitable for machine learning
Which patterns may influence future scaling decisions
How to prepare the dataset for demand forecasting and proactive infrastructure scaling
Task 1: Data Understanding and Validation
Objective
Before building any machine learning model, it is important to understand the data and verify that it is reliable.
The purpose of this task was to inspect the dataset, identify data quality issues, understand traffic behavior, and validate the target variables.
Activities Performed
Dataset Inspection
Reviewed:
Number of rows and columns
Available features
Data types
Time coverage of the monitoring data
Traffic Analysis
Analyzed the distribution of:
Active Users
Requests Per Minute
Orders Per Minute
CPU Utilization
Memory Utilization
Network Throughput
Response Time
This helped identify normal operating conditions as well as unusually high demand periods.
Target Analysis
Reviewed both machine learning targets:
Regression Target
```text
next_15min_requests
```
Used later to forecast short-term demand.
Classification Target
```text
scale_up_required
```
Used later to determine whether infrastructure scaling is required.
The analysis showed that approximately 26% of records require scale-up actions, indicating a moderate class imbalance.
Missing Value Assessment
Identified missing values across all columns and quantified the impact.
Observation:
Very few missing values were present.
Missing values were concentrated in infrastructure utilization metrics.
Outlier Review
Investigated unusual values using descriptive statistics and boxplots.
Rather than automatically removing extreme observations, they were reviewed in the context of business operations.
High values in:
Active Users
Requests Per Minute
Network Throughput
were considered valid because the dataset includes Sale and Flash Sale periods where demand surges are expected.
Key Findings
The dataset contains clear traffic variations across different sales periods.
Request volume and active user counts show strong positive skew caused by traffic spikes.
CPU utilization remains consistently high during busy periods.
The scale-up target is moderately imbalanced.
Missing values were limited and manageable.
Extreme demand observations appear to represent genuine business events rather than data quality issues.
Task 2: Data Preparation for Machine Learning
Objective
Raw monitoring data cannot be directly used by machine learning algorithms.
The goal of this task was to transform the dataset into a machine-learning-ready format while preserving operational meaning.
Activities Performed
Time-Based Feature Engineering
The timestamp column was converted into useful operational features:
Hour of Day
Day of Week
Weekend Indicator
These features help capture recurring demand patterns throughout the day and week.
Missing Value Treatment
Missing numerical values were imputed using the median value of their respective columns.
Median imputation was selected because several metrics contain skewed distributions and occasional spikes.
Categorical Encoding
The following categorical variables were transformed into a numerical format:
sale_event
region
One-Hot Encoding was applied so that machine learning models can effectively use these attributes.
Outlier Assessment
Outliers were reviewed using the IQR method.
Potential outlier observations were retained because they represent realistic traffic spikes during Sale and Flash Sale periods, which are critical scenarios for infrastructure scaling analysis.
Scaling Strategy
Scaling was evaluated based on the algorithm requirements.
Linear Regression will require scaling during model training.
Tree-based algorithms do not require feature scaling.
This approach ensures that transformations are only applied where they add value.
Target Separation
Targets were separated from the predictor features before model development.
Regression Target:
```text
next_15min_requests
```
Classification Target:
```text
scale_up_required
```
This prevents target leakage and ensures realistic model evaluation.
Key Outcomes
The dataset is now prepared for machine learning analysis through:
Time feature creation
Missing value handling
Categorical encoding
Outlier assessment
Target separation
All preprocessing decisions were documented and performed using only information available at the prediction time, ensuring that future modelling remains operationally realistic.
Technologies Used
Python
Pandas
NumPy
Matplotlib
Seaborn
Scikit-Learn
