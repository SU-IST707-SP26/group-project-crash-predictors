# NYC Traffic Crash Severity Prediction and Risk Modeling System

## Team

- **Adelina Dunina** (GitHub: Adelina2302) — *Point of Contact*
- **Arya Patil**

## Introduction

Every day in New York City, thousands of traffic crashes occur across five boroughs. Most are minor — a fender bender, a scratched bumper. But thousands every year result in serious injuries or fatalities, and for the people involved, the speed of emergency response can make the difference between life and death.

NYC Emergency Medical Services faces a real operational challenge: they have limited ambulances and personnel, and they need to decide where to position them before crashes happen. Right now, that decision is largely based on historical patterns and dispatcher experience. There's no system that tells EMS — "tonight, between 4 and 7pm, Brooklyn and Queens are your highest-risk zones, and cyclists are particularly vulnerable."

That's what we set out to build. Our system predicts, for each borough and time window, how likely a crash is to result in injury or fatality, how many crashes to expect per shift, and which road users — pedestrians, cyclists, or motorists — are most at risk. Predictions are aggregated by shift window — morning rush, midday, evening rush, night — so EMS can adjust deployment at the start of each shift rather than reacting after crashes occur.

We trained multiple machine learning models on 375,000 NYC crash records from 2022 to 2026, using temporal features (hour, day, season), spatial features (borough), and crash context (vehicle type). Logistic Regression emerged as the best model for EMS purposes — it catches 73% of all injury crashes, which matters most when missing a severe crash has real consequences. We also built a hybrid model combining Logistic Regression and LightGBM, and a Poisson regression component to estimate expected crash volumes per shift.

The result is not a perfect real-time prediction system — that would require weather data, live traffic feeds, and significantly more infrastructure. But it is a meaningful step in that direction: a shift-level risk forecasting tool that EMS can use today to make smarter decisions about where to be. A fully operational EMS deployment system would additionally require real-time weather data, live traffic feeds, and integration with dispatch infrastructure — areas we identify as clear next steps.

## Literature Review

Traffic collision research has come a long way — from simply mapping where crashes happen to trying to predict when and where the next serious one will occur. Understanding this progression is important for explaining why we built what we built.

Early approaches mostly used GIS-based hotspot maps to identify dangerous road segments based on historical crash counts. These are useful for understanding the past, but they can't tell you anything about what's coming next. For EMS, this is a critical limitation — knowing that an intersection was dangerous last year doesn't help a dispatcher decide where to position ambulances tonight.

Spatial clustering methods like K-means and DBSCAN take this a step further by grouping crash locations into high-risk zones. Anderson (2009) applies these techniques to classify accident hotspots, but the problem is they largely ignore time — a dangerous intersection at 5pm on a Friday is very different from the same intersection at 3am on a Sunday. EMS staffing decisions are made by shift, not by location alone.

Some researchers have tackled the time dimension using ARIMA models, which forecast crash counts based on historical trends. Wang et al. (2018) demonstrate that temporal autocorrelation can be leveraged for short-term accident prediction. But ARIMA is a fairly rigid tool — it assumes patterns are linear and can't incorporate other factors like vehicle type, borough, or driver behavior.

Tree-based ensemble methods have shown more promise. Santos et al. (2021) demonstrate that Random Forest and XGBoost can effectively capture the complex, non-linear relationships between crash features and severity outcomes. This makes them well-suited for our problem, where severity is influenced by a combination of time, place, and crash context simultaneously.

What's missing in most existing work is a model that combines all three dimensions at once: where, when, and why crashes happen. That's what we set out to build — specifically for EMS, who need to know not just where crashes occurred historically, but when and where severe crashes are most likely to happen next shift.

Our approach combines temporal features (hour, day, season), spatial features (borough, location), and crash context (vehicle type, contributing factors) into a single predictive system. We tested Logistic Regression as an interpretable baseline, then moved to Random Forest, XGBoost, and LightGBM to capture non-linear patterns. We also built a hybrid model combining the high recall of Logistic Regression with the predictive power of LightGBM. Finally, we added Poisson regression to predict expected crash counts per shift — giving EMS both a severity risk score and a volume estimate for each zone and time window.

## Data

This project uses the NYC Motor Vehicle Collisions — Crashes dataset published on the NYC Open Data Portal (https://data.cityofnewyork.us/Public-Safety/Motor-Vehicle-Collisions-Crashes/h9gi-nx95). The dataset is collected and maintained by the NYPD from standardized MV-104AN police collision reports, which are required for all crashes involving injury, fatality, or significant property damage in New York City.

Our working dataset contains 375,025 collision records spanning February 2022 to January 2026, with 28–29 raw attributes per record depending on missingness handling. Each record represents a single traffic collision event.

The dataset includes three main categories of variables:

Temporal features: crash date, crash time, hour, day of week, month, season, weekend indicator  
Spatial features: borough, latitude, longitude, street names, ZIP code (partial coverage)  
Crash characteristics: contributing factors (e.g., distraction, failure to yield), vehicle types involved, number of vehicles, and casualty counts  

The primary modeling target is a binary severity classification variable:

0 → Property Damage Only  
1 → Injury or Fatal Collision  

But we go beyond a single binary classifier. Predictions are aggregated by borough and shift window (morning rush, midday, evening rush, night) to give EMS a shift-level risk picture for each zone and time period. A Poisson model estimates expected crash counts per shift, so EMS knows not just how dangerous a zone is but how busy it's likely to be. Separate models also predict which road user type is most at risk — pedestrian, cyclist, or motorist — helping dispatchers decide what kind of response to prepare.

Tho overally dataset is moderately imbalance as a result of this modeling target with ~58% being Property Damage Only while 42% is Injury or Fatal. Fatal crashes alone represent a very small fraction (~0.27%), which motivates grouping injury and fatal outcomes into a single high-severity class for predictive stability and operational usefulness.

This dataset is considered high quality due to the fact it is officially collected by the NYPD and not self reported or crowd sourced. Furthermore it follows standarzied reporting forms. In addition to this it is continuously updated and used by NYC for transportation planning. Although this dataset has many spatial, temporal and descriptive factors that make it sutiable for predictive modeling, it does have issues in regards to missing borough values, geographic coordinates and “unspecified” contributing to a number of fields.
<img width="1349" height="468" alt="Screenshot 2026-05-04 at 6 37 15 PM" src="https://github.com/user-attachments/assets/d6515587-fdf7-46cd-82bf-ca21fcdeee23" />
<img width="1343" height="477" alt="Screenshot 2026-05-04 at 6 37 57 PM" src="https://github.com/user-attachments/assets/3065d378-e772-4ae5-97da-f0a6ed7deb84" />
<img width="1295" height="674" alt="Screenshot 2026-05-04 at 6 38 45 PM" src="https://github.com/user-attachments/assets/1a865174-dde7-4937-9e10-05c8a047c137" />
<img width="1274" height="588" alt="Screenshot 2026-05-04 at 6 38 15 PM" src="https://github.com/user-attachments/assets/e5058d0b-5e23-4532-870d-8fabd8643f2c" />


## Methods

Our modeling approach addresses the EMS deployment problem through three complementary components. The primary task is a supervised binary classification: predict whether a collision will result in injury or fatality (1) versus property damage only (0). Beyond this, we train separate classifiers to predict injury risk for each road user type — pedestrians, cyclists, and motorists — and a Poisson regression model to estimate expected crash counts per borough and shift window. Together these components produce a shift-level risk forecast rather than a single binary output per crash. The overall pipeline consists of five stages: data exploration, preprocessing and cleaning, feature engineering, model training and comparison, and evaluation.

### Data Preprocessing

The dataset was first cleaned and standardized to ensure consistency and usability. Crash dates were converted into datetime format, and derived temporal variables such as hour of day were extracted. Categorical variables — including borough, vehicle type, contributing factor, and season — were standardized, and missing values were filled with "Unknown" to preserve observations rather than dropping records.

One significant challenge was missing coordinates — about 28% of records had no latitude/longitude. Rather than using an external geocoding API (which would have been extremely slow at ~1 request per second for thousands of records), we built a self-lookup table from records that already had valid coordinates, grouped by street intersection. Missing coordinates were filled by matching on street name combinations in both forward and reverse order, with ZIP code centroids as a final fallback. This approach recovered approximately 15,000 of the 28,415 missing coordinate records, bringing coordinate coverage from 92.7% to 96.4%.

Contributing factors (e.g., "Driver Inattention", "Failure to Yield") were initially included as features but later removed after identifying them as a source of data leakage. These values are recorded by police officers after arriving at the crash scene and would not be available at prediction time in a real deployment setting. Including them would have artificially inflated model performance.

Class imbalance was addressed using cost-sensitive weighting rather than oversampling. We initially attempted SMOTE to generate synthetic minority-class samples, but this caused memory crashes on our 375,000-record dataset — the synthetic oversampling nearly doubled the size of the training data, exceeding available memory. We switched to `class_weight='balanced'` in Logistic Regression and `scale_pos_weight` in XGBoost and LightGBM, which assigns a higher penalty to misclassifying the minority class without increasing dataset size. This produced comparable improvements in recall without the computational overhead.

A temporal train-test split was used to prevent data leakage and simulate real-world forecasting conditions. Data from 2022–2023 was used for training, while data from 2024–2026 was reserved for testing. This ensures that models are evaluated on future unseen data rather than randomly shuffled samples.

Categorical variables were encoded using one-hot encoding, and only numerical or encoded features were retained for modeling. Leakage-prone variables, such as injury counts, fatality indicators, and other target-derived fields, were excluded to ensure that predictions rely only on features available at inference time.

### Feature Engineering

Features were engineered across three domains. Temporal features included hour of day, day of week, month, season, weekend flag, and a derived time interval (morning rush, midday, evening rush, night, late night). Spatial features included borough-level one-hot indicators, capturing geographic differences in traffic patterns and infrastructure. Behavioral features included vehicle type categories and the number of vehicles involved. Contributing factors were excluded due to leakage concerns described above. All categorical variables were encoded using one-hot encoding, and numerical features were standardized using StandardScaler for Logistic Regression.

### Modeling Approach

We implemented a multi-model comparison framework using both linear and non-linear models. Logistic Regression was used as a baseline model, implemented within a pipeline that includes feature scaling via StandardScaler and class balancing using `class_weight='balanced'`. This model provides interpretability and strong baseline performance and, as we later found, the highest recall among all models — which is the most important metric for EMS.

Tree-based models were used to capture non-linear relationships and feature interactions. Random Forest was trained both on the full dataset and on a reduced feature set selected based on feature importance thresholds. Gradient boosting models, including XGBoost and LightGBM, were also implemented due to their strong performance on structured tabular data. These models handled class imbalance using a `scale_pos_weight` parameter, defined as the ratio of negative to positive samples.

Different feature input strategies were used depending on the model type. Logistic Regression was trained on scaled features, while tree-based models were trained on unscaled data to preserve feature structure. Random Forest additionally used a feature-selected subset to reduce noise and improve efficiency.

### Hybrid Model

In addition to individual models, we developed a hybrid modeling approach that combines Logistic Regression and LightGBM using a confidence-based decision rule. The intuition behind this approach is that Logistic Regression provides reliable predictions when it is highly confident, while LightGBM performs better in capturing complex, non-linear relationships when uncertainty is higher.

For each prediction, the Logistic Regression model outputs a probability score. If this probability exceeds a predefined confidence threshold (0.6 for the positive class or below 0.4 for the negative class), the Logistic Regression prediction is used. If the prediction falls within the uncertainty region (between 0.4 and 0.6), the model defers to the LightGBM prediction. This creates a dynamic system that leverages the strengths of both linear interpretability and non-linear modeling.

To ensure proper evaluation, the hybrid model was validated using stratified K-fold cross-validation with out-of-fold predictions. This avoids data leakage and provides an unbiased estimate of performance.

### Road User Risk Models

Beyond overall severity, we trained separate Logistic Regression models to predict injury risk for each road user type: pedestrians, cyclists, and motorists. Each model uses the same feature set but a different binary target — for example, "was a pedestrian injured in this crash?" or "was a cyclist injured?" This allows EMS to see not just overall risk but which type of response is most likely needed in a given zone and shift. A cyclist-heavy zone during evening rush hours requires a different response than a pedestrian-heavy zone in a dense commercial area.

### Poisson Regression for Crash Volume

To complement the severity classifiers, we fit a Poisson regression model to predict expected crash counts per borough and time window per shift. Poisson regression is well-suited for count data and is commonly used in transportation demand modeling. The model was trained on collision counts aggregated by borough, day of week, time interval, month, and weekend flag, using a temporal split consistent with the classification models. This gives EMS a volumetric estimate — not just how dangerous a zone is, but how busy it is likely to be.

### Shift-Level Forecast

Individual crash-level predictions from the severity and road user models are aggregated by borough and shift window to produce a shift-level risk table. For each combination of borough and time period, EMS sees the predicted injury risk percentage, expected crash volume from the Poisson model, and breakdown of pedestrian, cyclist, and motorist risk. This is the primary operational deliverable of the system — a concrete, actionable table that EMS dispatchers can use at the start of each shift to decide where to pre-position ambulances and what type of response to prepare.

### Evaluation Strategy

Model performance was evaluated using precision, recall, F1-score, and ROC-AUC. Accuracy alone was not considered sufficient given the class imbalance in the dataset. Given the application to emergency medical services, recall for injury and fatal collisions was prioritized — failing to identify a high-severity crash carries a higher operational cost than a false positive, since over-deploying an ambulance wastes resources but missing a severe crash can cost lives.

Stratified K-fold cross-validation (3–5 folds) was used to ensure robustness and maintain class balance across splits. Both mean performance and standard deviation across folds were reported to assess model stability and generalizability. For the hybrid model specifically, out-of-fold predictions were used to avoid data leakage during cross-validation.

Confusion matrices were generated for all models to visualize the trade-off between false positives and false negatives. Feature importance was extracted to improve interpretability — Logistic Regression coefficients were used to identify linear feature influence, while feature importance scores from tree-based models captured non-linear relationships. This enables comparison of key drivers of crash severity across different modeling approaches and provides actionable insight for EMS planners about which factors most strongly predict severe outcomes.

## Supporting files

All supporting notebooks are located in the `work` folder. Each notebook corresponds to a specific stage of the pipeline and is designed to be run in order. The outputs of earlier notebooks (e.g., cleaned datasets) are used as inputs to later ones.

- **`01_initial_eda.ipynb`** — Initial exploratory data analysis. Covers dataset overview (shape, column types, missing values), temporal pattern analysis (collisions by hour, day, month, year), spatial analysis (borough distributions, coordinate coverage), severity distribution, and contributing factor frequency. Key finding: collision peak at 5PM, Friday is the most dangerous day, Brooklyn accounts for 34.8% of all crashes.

- **`02_milestone_data_cleaning.ipynb`** — Full data cleaning pipeline. Handles coordinate formatting (European-style comma decimals), missing coordinate recovery via intersection lookup table (forward match, reverse match, ZIP centroid fallback), contributing factor standardization and grouping into 15 categories, vehicle type cleaning, and severity classification. Outputs `collisions_clean.csv` used by all subsequent notebooks.

- **`03_milestone_feature_engineering.ipynb`** — Feature engineering and dataset preparation. Creates temporal features (hour, day of week, month, season, weekend flag, time interval), one-hot encodes categorical variables (borough, vehicle type, season, time interval), defines binary and multi-class target variables, and produces the temporal train/test split (2022–2023 train, 2024–2026 test).

- **`04_milestone_baseline_modeling.ipynb`** — Baseline model training and evaluation. Trains Logistic Regression with StandardScaler and class balancing, evaluates on test set with classification report, confusion matrix, and ROC curve. Also fits a Poisson regression model to predict collision counts by borough and time window. Establishes baseline metrics for comparison with later models.

- **`05_milestone_model_development.ipynb`** — Tree-based model development. Trains Random Forest (full and feature-selected), XGBoost, and LightGBM. Includes GridSearch hyperparameter tuning for Random Forest and full model comparison table across all metrics (accuracy, precision, recall, F1, ROC-AUC).

- **`06_milestone_model_refinement.ipynb`** — Model refinement and hybrid modeling. Develops the confidence-based hybrid model combining Logistic Regression and LightGBM. Tests alternative class imbalance strategies including SMOTE (abandoned due to memory crashes) and cost-sensitive weighting. Runs stratified K-fold cross-validation for all models and reports mean and standard deviation across folds.

- **`07_milestone_model_visualization.ipynb`** — Visualization dashboard. Produces heatmaps of injury rate by borough and time of day, borough and contributing factor, and hour by day of week. Includes geographic scatter plots of collision locations colored by severity, interactive Folium heatmap of injury crashes, confusion matrices, ROC curves, and model performance comparison charts.

- **`08_milestone_ems_dashboard.ipynb`** — EMS shift-level forecast system. Trains the final Logistic Regression model and attaches predicted risk scores to each crash in the test set. Trains separate road user risk models for pedestrians, cyclists, and motorists. Fits Poisson regression for expected crash volume per shift. Aggregates all predictions by borough and time window to produce the final EMS shift planning table, heatmap, and priority matrix (risk score vs. expected volume).

## Results

We evaluate multiple supervised learning models to predict whether a traffic collision in NYC results in injury or fatality (class = 1) versus property damage only (class = 0). All models are evaluated using a temporal holdout split, where training is performed on 2022–2023 data and testing is conducted on 2024–2026 data. This ensures the results reflect true forward-looking predictive performance rather than random sampling effects. Because the dataset is moderately imbalanced (approximately 40% injury/fatal and 60% property damage), we report precision, recall, F1-score, and ROC-AUC, as accuracy alone does not adequately capture performance. Recall is especially important for the EMS use case, since failing to identify a severe crash is more costly than a false positive.

### Binary Severity Classification

The Logistic Regression model serves as a linear baseline with standardized features and class weighting to address imbalance. It achieves a recall of 0.7297, the highest among all individual models, indicating strong ability to identify injury and fatal crashes. However, this comes at the expense of lower precision (0.5345), leading to more false positives. Overall, it produces an F1-score of 0.6170 and ROC-AUC of 0.6860. In contrast, the Random Forest model trained with feature selection performs more conservatively, achieving higher precision (0.5846) but significantly lower recall (0.4382), resulting in weaker overall utility for emergency response prediction.

The gradient boosting models, XGBoost and LightGBM, show improved overall performance and stronger generalization. XGBoost achieves an F1-score of 0.5999 and ROC-AUC of 0.7020, while LightGBM produces similar results with an F1-score of 0.5965 and ROC-AUC of 0.7017. Both models outperform the classical baselines in overall discriminative ability, indicating that nonlinear relationships between spatial, temporal, and behavioral features are important for predicting collision severity.

To further improve performance, we construct a hybrid ensemble model combining Logistic Regression and LightGBM using a confidence-based gating mechanism. Logistic Regression is used for high-confidence predictions due to its strong recall behavior, while LightGBM handles uncertain cases where nonlinear interactions are more important. This hybrid model achieves the highest accuracy (0.6565) and ROC-AUC (0.7037), with an F1-score of 0.5768 and recall of 0.5824. However, it does not outperform XGBoost or LightGBM in recall or F1-score, indicating that while it improves overall stability and balance, it does not maximize sensitivity to severe crashes.

Cross-validation results further confirm model stability. LightGBM and XGBoost consistently perform best across folds with ROC-AUC values around 0.70 and low variance, indicating strong generalization. Logistic Regression maintains higher recall but shows less stability in precision, while Random Forest consistently underperforms relative to other models.

Overall, no single model dominates across all metrics. Logistic Regression provides the strongest recall and is most suitable for maximizing coverage of severe crashes, while gradient boosting models provide the strongest overall predictive performance. The hybrid model offers a balanced compromise with strong ROC-AUC but does not outperform boosting methods in identifying severe crashes. For the EMS use case, this suggests a trade-off between recall-focused linear approaches and more globally accurate nonlinear models depending on operational priorities.

Cross Validation Results
<img width="1071" height="150" alt="Cross Validation Results" src="https://github.com/user-attachments/assets/db9d616d-e57e-41f0-8af1-9f9fa24370d2" />

Test Results
<img width="553" height="97" alt="Test Results" src="https://github.com/user-attachments/assets/959beb9b-90b4-46f3-a387-91901bcf7184" />

Hybrid Model Cross Validation
<img width="278" height="103" alt="Hybrid Model Cross Validation" src="https://github.com/user-attachments/assets/799ff167-3959-49af-b1f6-0b5db514221a" />

<img width="797" height="441" alt="Model Comparison" src="https://github.com/user-attachments/assets/8e2a518f-03fd-4221-9e08-0c78920dce0d" />

Confusion matrices and ROC curves for all models are available in `07_milestone_model_visualization.ipynb`.

### Feature Importance

Across all models, feature importance analysis highlights consistent predictors of collision severity, including time of day (particularly rush hours), geographic location (ZIP code and borough), number of vehicles involved, vehicle type (such as SUVs, motorcycles, and E-bikes), and contributing behavioral factors such as distracted driving, failure to yield, and improper lane use. These patterns are consistent across both linear and tree-based models, reinforcing their predictive relevance. Full feature importance visualizations for all models are available in `05_milestone_model_development.ipynb` and `07_milestone_model_visualization.ipynb`.

### Poisson Regression — Expected Crash Volume

To complement the severity classifiers, we fit a Poisson regression model to predict the expected number of crashes per borough and shift window. The model achieves a Mean Absolute Error (MAE) of approximately 3.47 crashes per shift against a mean actual count of 7.79 crashes per shift — an error rate of roughly 44.6%. While this is a moderate error, it provides EMS with a useful order-of-magnitude estimate of crash volume that can inform staffing decisions. For example, Brooklyn during evening rush hours is predicted to see approximately 23 crashes per shift, of which roughly 63% are predicted to result in injury or fatality. Full Poisson results are available in `08_milestone_ems_dashboard.ipynb`.

### Road User Risk Models

Separate Logistic Regression models were trained to predict injury risk for each road user type. Results show that cyclist injury risk is highest during evening rush hours (approximately 52% in Brooklyn), while pedestrian injury risk is more evenly distributed across time windows but elevated in Manhattan and Brooklyn. Motorist injury risk is consistently the highest across all boroughs and time windows, reflecting the volume of vehicle-to-vehicle crashes. These results allow EMS to anticipate not just whether a crash will be severe, but what type of response — trauma unit, standard ambulance, or specialized care — is most likely needed.

### Shift-Level EMS Forecast

The primary operational output of the system is a shift-level risk table aggregated by borough and time window. The table below shows the top predicted risk zones for EMS deployment based on the 2024–2026 test period:

| Borough | Time Window | Injury Risk % | Cyclist Risk % | Pedestrian Risk % | Motorist Risk % |
|---------|-------------|--------------|----------------|-------------------|-----------------|
| Brooklyn | Evening Rush | 63.8% | 51.8% | 37.0% | 54.2% |
| Brooklyn | Night | 62.4% | 48.1% | 34.1% | 56.3% |
| Queens | Evening Rush | 63.1% | 40.6% | 38.6% | 55.3% |
| Bronx | Evening Rush | 62.7% | 37.5% | 37.4% | 57.0% |
| Manhattan | Evening Rush | 61.5% | 45.2% | 42.1% | 53.8% |

These results show that evening rush hours consistently represent the highest-risk period across all boroughs, with Brooklyn showing the highest overall injury risk. Cyclist risk is particularly elevated in Brooklyn, suggesting that EMS should be prepared for bike-related trauma during evening shifts. Full shift-level results and visualizations are available in `08_milestone_ems_dashboard.ipynb`.


## Discussion

Our primary goal was to build a predictive system that helps NYC Emergency Medical Services make smarter decisions about ambulance positioning and shift planning. To what degree did we achieve this? Partially — and we think meaningfully so, even if the system is not yet ready for real-time operational deployment.

On the classification side, we successfully built a model that catches 73% of all injury and fatal crashes — the highest recall among all models we tested. For EMS, this is the metric that matters most: missing a severe crash is more costly than a false alarm. The trade-off is lower precision (0.53), meaning the model also flags some crashes that turn out to be minor. This is an acceptable cost for EMS, where over-deploying an ambulance wastes resources but does not cost lives.

What we are most proud of, however, is going beyond a single binary classifier. The shift-level forecast system — which aggregates predictions by borough and time window — directly addresses the EMS operational need for shift-level planning. Rather than asking "will this specific crash be severe?", EMS can now ask "which zones should I prioritize for the next shift?" Brooklyn during evening rush hours consistently emerges as the highest-risk zone, with ~64% injury risk and ~52% cyclist involvement. This is the kind of actionable output that can inform real dispatch decisions.

The road user risk models add another layer of operational value. Knowing that a zone has elevated cyclist risk versus pedestrian risk helps EMS decide not just where to deploy, but what kind of response to prepare. The Poisson component gives EMS a volume estimate — not just how dangerous a zone is, but how busy it is likely to be.

That said, we want to be honest about what this system does not yet do. It does not predict where or when crashes will occur — it predicts the likely severity and user type given that a crash has occurred, and aggregates those predictions historically. A truly proactive system would require real-time inputs like weather, traffic volume, and live event data. Our system is best understood as a data-driven shift planning tool rather than a real-time dispatch system.

We also acknowledge that the models rely entirely on historical patterns. If traffic conditions, infrastructure, or reporting practices change significantly, model performance may degrade. The missing borough data (28.7% of records) limits spatial precision, and the absence of weather data is a known gap that likely affects model quality.

Overall, we believe this work represents a genuine and meaningful step toward data-driven EMS resource allocation in New York City — one that goes well beyond simple hotspot mapping while being honest about the distance still to travel toward a fully operational system.


## Limitations

Despite promising results, several limitations affect the robustness and practical deployment of this system. First, the dataset contains a significant amount of missing or incomplete spatial information, with a large portion of records lacking borough or precise coordinate data. Although we applied imputation strategies using intersection-level matching and ZIP centroids, these approximations introduce noise and may reduce spatial accuracy.

Second, the target formulation simplifies severity into a binary classification (injury/fatal vs. property damage). While this improves model stability, it collapses important distinctions between injury severity levels and fatal crashes, which are operationally very different for EMS response prioritization. The extreme rarity of fatal crashes (0.27%) also limits the model's ability to learn meaningful patterns specific to fatalities.

Third, while temporal splitting improves realism, the models still rely on historical patterns that may not fully capture evolving traffic conditions, policy changes, or external disruptions such as weather or construction. The absence of weather data is particularly notable — precipitation, temperature, and road surface conditions are strongly associated with crash severity and represent one of the most impactful missing features in our models. Additionally, some features (e.g., contributing factors) are derived from post-incident reporting and may not always be immediately available in real-time prediction settings, limiting deployment feasibility.

Fourth, although the hybrid model introduces a more flexible decision mechanism, its rule-based gating strategy is heuristic rather than learned, which may limit optimal performance compared to a fully trained stacking or meta-learning approach.

There are also reasons to be cautious about the quality of our results more broadly. The Poisson crash volume model achieves a MAE of ~44.6% relative to the mean, meaning crash count estimates should be treated as rough directional indicators rather than precise forecasts. Our models may also reflect reporting patterns as much as actual collision risk, since minor crashes may be systematically underreported in some neighborhoods.

Finally, while our shift-level forecast addresses the EMS need for proactive planning, it does not yet address the need for real-time updates. The system currently supports shift-level planning but cannot respond to live conditions — meaning one key EMS operational need remains unmet and represents the most important direction for future work.


## Future Work

The most impactful next step would be integrating real-time external data — particularly weather conditions (precipitation, temperature, road surface) and live traffic volume — which are strongly associated with crash severity and could significantly improve model performance. We would also like to extend the severity model from binary classification to multi-class prediction, distinguishing between property damage, injury, and fatal outcomes more precisely.
On the modeling side, the heuristic confidence thresholds in the hybrid model could be replaced with a learned stacking approach for better performance. We would also like to explore LSTM-based temporal models for shift-level forecasting, which could capture sequential patterns in crash data that our current models miss.
Finally, the most meaningful long-term extension would be integrating this system directly with EMS dispatch infrastructure — allowing predictions to update in real time and providing dispatchers with a live risk dashboard rather than a static shift-planning table.

### References

[1] Chen, Q., et al. "Road traffic safety analysis based on a GIS-based system." *International Journal of Transportation* 8.2 (2020): 45-58.

[2] Plug, C., et al. "Spatial and temporal visualisation techniques for crash analysis." *Accident Analysis & Prevention* 43.6 (2011): 1937-1946.

[3] Anderson, T. K. "Kernel density estimation and K-means clustering to profile road accident hotspots." *Accident Analysis & Prevention* 41.3 (2009): 359-364.

[4] Wang, L., et al. "Short-term traffic accident prediction based on ARIMA." *Transportation Research Record* 2672.38 (2018): 86-97.

[5] Santos, D., et al. "Predicting crash injury severity with machine learning: A comparative study." *Journal of Safety Research* 78 (2021): 207-221.