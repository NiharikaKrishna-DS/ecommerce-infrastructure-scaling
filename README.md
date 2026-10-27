Name: Niharika N K 
Project : # Ecommerce Infrastructure Scaling Analysis
Batch Code : 302602 ( Skillfyme )

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

## Task 6: Discover Traffic and Infrastructure States

### Objective
Identify natural operating states in the platform without using a target label, so the team can understand recurring infrastructure patterns and distinguish normal traffic from pre-scale or rare stress conditions.

### Activities
- Applied unsupervised clustering to workload and infrastructure features
- Compared clustering approaches:
  - K-Means
  - Hierarchical clustering
  - DBSCAN
- Evaluated cluster quality using silhouette analysis and operational interpretation
- Translated cluster patterns into actionable operational states for SRE and capacity planning teams

### Key findings
- K-Means produced the clearest and most actionable operating-state structure.
- The selected clustering model identified three primary states:
  - Low Traffic Steady State
  - Moderate Pressure Zone
  - High Traffic / Pre-Scale State
- Cluster 0 represented low-demand, stable platform conditions.
- Cluster 2 captured moderate demand with some operating stress but no immediate scaling emergency.
- Cluster 1 represented the most critical state: elevated workload, constrained headroom, and conditions that precede or coincide with scale-up decisions.
- Hierarchical clustering also revealed meaningful structure, but it was less practical for live operational categorization than K-Means.
- DBSCAN identified a dominant normal state plus several unusual observations, confirming that not all rare conditions should be treated as noise.

### Recommended approach
K-Means was chosen as the preferred clustering method because it offers a simple, interpretable, and operationally useful grouping of the telemetry data. It is especially suitable for classifying the platform into stable, moderate, and high-pressure operating states during day-to-day monitoring.

### Business value
This task helps NimbusCart move from reactive monitoring to state-aware operations management. By identifying when the platform is in a low-risk, moderate-risk, or pre-scale condition, SRE teams can better prioritize alerts, plan maintenance windows, and allocate capacity before demand spikes create customer-facing performance issues.

## Task 7: Reduce Dimensions and Visualize Results

### Objective
Reduce the high-dimensional infrastructure feature space into a two-dimensional representation and visualize the operational clusters so the structure of the data is easier to interpret.

### Activities
- Applied Principal Component Analysis (PCA) to the clustering feature set
- Reduced the data to a two-dimensional projection
- Plotted the K-Means clusters in PCA space
- Assessed whether low-demand and high-pressure operating states separated cleanly in the reduced feature space

### Key findings
- The first two principal components retained approximately 92.96% of the total variance.
- This indicates that the core operating behavior of the infrastructure can be represented effectively in a two-dimensional space.
- The PCA projection showed clear separation between the discovered clusters.
- Low-demand and high-pressure operating states occupied different regions of the PCA space, supporting the validity of the clustering solution.
- The reduced visualization makes it easier to interpret cluster boundaries and communicate infrastructure states to operations teams.

### Business value
PCA creates an intuitive, visual summary of the platform’s behavior. This helps SRE teams understand how different operating states differ and supports faster communication of cluster meaning during reviews, dashboards, and incident discussions.

## Task 8: Proactive Capacity Recommendation

### Objective
Translate the forecasting, classification, and clustering results into a practical operational recommendation for NimbusCart’s SRE and platform teams.

### Key recommendations
- Use the Random Forest classifier as the preferred production model for proactive scale-up decisions because it provides stronger recall and a better balance between reliability and cost.
- Treat the regression model as an early-warning demand forecast for the next 15 minutes to support capacity planning and lead-time decisions.
- Use the clustering outputs as contextual indicators of the current operating state, alongside model predictions, to understand whether the environment is stable, moderately stressed, or near a scale-up threshold.
- Prioritize attention during sale and flash sale windows, when the largest traffic spikes and the greatest prediction uncertainty were observed.

### Monitoring recommendation
- Increase monitoring intensity during promotional events and high-demand periods.
- Review workload, CPU, memory, network, and capacity-headroom metrics together rather than relying on a single signal.
- Monitor model drift and changing demand patterns over time, since infrastructure behavior may evolve with new product launches or seasonal campaigns.

### Alerting recommendations
Alerts should combine multiple signals instead of triggering on a single metric alone:
- Forecasted request volume above expected thresholds
- Scale-up prediction probability crossing a decision threshold
- Low capacity headroom or rising utilization pressure
- Cluster membership indicating a high-traffic or pre-scale state

This layered alerting strategy reduces false alarms while improving early detection of risky operating conditions.

### Operational guidance
- Missing a required scale-up creates greater availability risk than performing an unnecessary scale-up; therefore, the classifier should favor recall when operational decisions are uncertain.
- Predictions should support human decision-making rather than replace it.
- SRE judgment remains essential for final scale actions, especially during unusual promotions, outages, or operational incidents.

### Final outcome
Overall, the models provide useful early-warning signals for proactive capacity planning, but real-time telemetry and operational context should remain part of the final scaling decision. The combined approach gives NimbusCart a practical framework for anticipating demand surges, identifying operational states, and improving infrastructure resilience.

## Summary
This analysis shows that the system can support both forecasting and proactive actioning:
- The regression model estimates future request volume from current conditions.
- The classification model decides when scaling should be triggered before service degradation occurs.
- The clustering model identifies the platform’s recurring operational states and reveals when the infrastructure is nearing a scale-up threshold.
- The dimensionality reduction step makes those states easier to interpret and explain visually.
- The final capacity recommendation translates these findings into practical SRE actions and monitoring policies.
- Together, these outputs provide a practical basis for infrastructure planning, risk reduction, and operational decision support during high-demand periods.
