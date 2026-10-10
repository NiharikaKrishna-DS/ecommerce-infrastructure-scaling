

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

# task 3: Feature Engineering and feature selection

## Objective

Infrastructure metrics often become more useful when they are transformed into features that capture operatinal behaviour and 
system pressure 

