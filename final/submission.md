# NYC Traffic Crash Severity Prediction and Risk Modeling System

## Introduction

Traffic collisions in New York City represent a persistent and high-impact public safety challenge, particularly for emergency medical services (EMS) responsible for rapid response to severe crashes. In a dense urban environment where collision frequency varies dramatically across time, geography, and driving conditions, EMS resource allocation is often reactive rather than predictive. This creates inefficiencies in ambulance positioning and delays in reaching high-severity incidents.

The goal of this project is to develop a predictive modeling system that estimates whether a traffic collision in New York City will result in injury or fatality versus property damage only, enabling EMS to anticipate high-risk periods and locations. The primary stakeholder is NYC Emergency Medical Services (EMS), whose operational objective is to minimize response times to severe collisions by proactively positioning personnel and ambulances in high-risk zones.

Current EMS planning approaches rely heavily on historical hotspot mapping and descriptive analytics, which identify where crashes have occurred but do not provide predictive insight into when future high-severity collisions are likely to happen. This limits their ability to adapt to real-time shifts in traffic patterns, temporal risk factors, or evolving road conditions.

To address this gap, we build a supervised machine learning classification system using a large-scale NYC collision dataset spanning 2022–2026. The model integrates temporal features (hour, weekday, season), spatial features (borough, coordinates), and behavioral factors (driver distraction, failure to yield, vehicle type) to predict collision severity.

The intended impact of this work is not to replace EMS decision-making, but to augment it with a predictive risk layer that can inform shift scheduling, ambulance positioning, and resource allocation at finer temporal resolution (hourly or daily). Even moderate improvements in recall for injury/fatal collisions can translate into meaningful reductions in emergency response time in high-density urban environments like New York City.

## Literature Review

Traffic collision analysis has been extensively studied across transportation engineering, urban informatics, and machine learning, with methods evolving from static spatial analysis to more dynamic predictive modeling approaches.

Traditional approaches to collision analysis primarily rely on geospatial hotspot detection methods such as kernel density estimation and spatial clustering. Chen et al. (2020) demonstrate the use of GIS-based systems to identify high-risk road segments by aggregating historical crash frequencies. While effective for retrospective analysis, these approaches are inherently descriptive and cannot predict future collision severity or account for temporal variation in risk factors.

Clustering-based methods, such as K-means and DBSCAN, have also been widely used to identify spatial patterns in crash data. Anderson (2009) applies clustering techniques to classify road accident hotspots, highlighting their usefulness in identifying spatial concentration areas. However, these methods treat time as a secondary or ignored dimension, limiting their applicability for real-time EMS deployment decisions.

To incorporate temporal structure, some studies have explored time-series forecasting models such as ARIMA. Wang et al. (2018) apply ARIMA-based approaches to short-term traffic accident prediction, demonstrating that temporal autocorrelation can be leveraged to forecast collision counts. However, ARIMA models assume linear temporal relationships and struggle to incorporate heterogeneous feature types such as road geometry, vehicle characteristics, or behavioral contributing factors.

Despite these advancements, a key limitation in existing literature is the lack of unified models that simultaneously incorporate temporal dynamics, spatial context, and behavioral factors into a single predictive framework. Many prior studies either focus on spatial clustering without prediction, or time-series forecasting without rich feature engineering.

Our approach builds on this literature by integrating all three dimensions into a single supervised learning pipeline. Unlike hotspot mapping or purely temporal forecasting models, we explicitly model collision severity as a function of:

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

Tho overally dataset is moderately imbalance as a result of this modeling target with ~58% being Property Damage Only while 42% is Injury or Fatal. Fatal crashes alone represent a very small fraction (~0.27%), which motivates grouping injury and fatal outcomes into a single high-severity class for predictive stability and operational usefulness.

This dataset is considered high quality due to the fact it is officially collected by the NYPD and not self reported or crowd sourced. Furthermore it follows standarzied reporting forms. In addition to this it is continuously updated and used by NYC for transportation planning. Although this dataset has many spatial, temporal and descriptive factors that make it sutiable for predictive modeling, it does have issues in regards to missing borough values, geographic coordinates and “unspecified” contributing to a number of fields.
<img width="1349" height="468" alt="Screenshot 2026-05-04 at 6 37 15 PM" src="https://github.com/user-attachments/assets/d6515587-fdf7-46cd-82bf-ca21fcdeee23" />
<img width="1343" height="477" alt="Screenshot 2026-05-04 at 6 37 57 PM" src="https://github.com/user-attachments/assets/3065d378-e772-4ae5-97da-f0a6ed7deb84" />
<img width="1295" height="674" alt="Screenshot 2026-05-04 at 6 38 45 PM" src="https://github.com/user-attachments/assets/1a865174-dde7-4937-9e10-05c8a047c137" />
<img width="1274" height="588" alt="Screenshot 2026-05-04 at 6 38 15 PM" src="https://github.com/user-attachments/assets/e5058d0b-5e23-4532-870d-8fabd8643f2c" />


## Methods

We ultimately went about creating the model as a supervised binary classification task, where the goal is to predict whether a collision will result in injury/fatality (1) or property damage only (0) based on contextual, spatial, and behavioral features available at or before the time of the crash. The overall pipeline consists of multiple steps this includes data exploration, data preprocessing and cleaning, feature engineering, model training using multiple classifiers, and evaluation using classification metrics.

## Data Preprocessing

The dataset was first cleaned and standardized to ensure consistency and usability. Crash dates were converted into datetime format, and derived temporal variables such as hour of day were extracted. Categorical variables—including borough, vehicle type, contributing factor, and season—were standardized, and missing values were filled with “Unknown” to preserve observations.

A temporal train-test split was used to prevent data leakage and simulate real-world forecasting conditions. Data from 2022–2023 was used for training, while data from 2024–2026 was reserved for testing. This ensures that models are evaluated on future unseen data rather than randomly shuffled samples.

Categorical variables were encoded using one-hot encoding, and only numerical or encoded features were retained for modeling. Leakage-prone variables, such as injury counts, fatality indicators, and other target-derived fields, were excluded to ensure that predictions rely only on features available at inference time.

## Feature Engineering

Features were engineered across three domains. Temporal features included hour of day, day of week, month, season, and a derived time interval (e.g., morning rush, midday, evening rush, night). Spatial features included borough-level indicators, capturing geographic differences in traffic patterns and infrastructure. Behavioral features included contributing factor categories (e.g., distracted driving, failure to yield), vehicle type categories, and the number of vehicles involved. These features represent driver behavior and crash context, which are strongly associated with severity outcomes.

## Modeling Approach

We implemented a multi-model comparison framework using both linear and non-linear models. Logistic Regression was used as a baseline model, implemented within a pipeline that includes feature scaling via StandardScaler and class balancing using class_weight = “balanced.” This model provides interpretability and strong baseline performance.

Tree-based models were used to capture non-linear relationships and feature interactions. Random Forest was trained both on the full dataset and on a reduced feature set selected based on feature importance thresholds. Gradient boosting models, including XGBoost and LightGBM, were also implemented due to their strong performance on structured tabular data. These models handled class imbalance using a scale_pos_weight parameter, defined as the ratio of negative to positive samples.

Different feature input strategies were used depending on the model type. Logistic Regression was trained on scaled features, while tree-based models were trained on unscaled data to preserve feature structure. Random Forest additionally used a feature-selected subset to reduce noise and improve efficiency.

## Hybrid Model

In addition to individual models, we developed a hybrid modeling approach that combines Logistic Regression and LightGBM using a confidence-based decision rule. The intuition behind this approach is that Logistic Regression provides reliable predictions when it is highly confident, while LightGBM performs better in capturing complex, non-linear relationships when uncertainty is higher.

For each prediction, the Logistic Regression model outputs a probability score. If this probability exceeds a predefined confidence threshold (0.6 for the positive class or below 0.4 for the negative class), the Logistic Regression prediction is used. If the prediction falls within the uncertainty region (between 0.4 and 0.6), the model defers to the LightGBM prediction. This creates a dynamic system that leverages the strengths of both linear interpretability and non-linear modeling.

To ensure proper evaluation, the hybrid model was validated using stratified K-fold cross-validation with out-of-fold predictions. This avoids data leakage and provides an unbiased estimate of performance.

## Evaluation Strategy

Model performance was evaluated using accuracy, precision, recall, F1-score, and ROC-AUC. Given the application to emergency medical services, recall for injury and fatal collisions was prioritized, as failing to identify a high-severity crash carries a higher operational cost than false positives.

Stratified K-fold cross-validation (3–5 folds) was used to ensure robustness and maintain class balance across splits. Both mean performance and standard deviation across folds were reported to assess model stability.

Feature importance was extracted to improve interpretability. Logistic Regression coefficients were used to identify linear feature influence, while feature importance scores from tree-based models were used to identify non-linear relationships. This enables comparison of key drivers of crash severity across different modeling approaches.

## Results

We evaluate multiple supervised learning models to predict whether a traffic collision in NYC results in injury or fatality (class = 1) versus property damage only (class = 0). All models are evaluated using a temporal holdout split, where training is performed on 2022–2023 data and testing is conducted on 2024–2026 data. This ensures the results reflect true forward-looking predictive performance rather than random sampling effects. Because the dataset is moderately imbalanced (approximately 40% injury/fatal and 60% property damage), we report precision, recall, F1-score, and ROC-AUC, as accuracy alone does not adequately capture performance. Recall is especially important for the EMS use case, since failing to identify a severe crash is more costly than a false positive.

The Logistic Regression model serves as a linear baseline with standardized features and class weighting to address imbalance. It achieves a recall of 0.7297, the highest among all individual models, indicating strong ability to identify injury and fatal crashes. However, this comes at the expense of lower precision (0.5345), leading to more false positives. Overall, it produces an F1-score of 0.6170 and ROC-AUC of 0.6860. In contrast, the Random Forest model trained with feature selection performs more conservatively, achieving higher precision (0.5846) but significantly lower recall (0.4382), resulting in weaker overall utility for emergency response prediction.

The gradient boosting models, XGBoost and LightGBM, show improved overall performance and stronger generalization. XGBoost achieves an F1-score of 0.5999 and ROC-AUC of 0.7020, while LightGBM produces similar results with an F1-score of 0.5965 and ROC-AUC of 0.7017. Both models outperform the classical baselines in overall discriminative ability, indicating that nonlinear relationships between spatial, temporal, and behavioral features are important for predicting collision severity.

To further improve performance, we construct a hybrid ensemble model combining Logistic Regression and LightGBM using a confidence-based gating mechanism. Logistic Regression is used for high-confidence predictions due to its strong recall behavior, while LightGBM handles uncertain cases where nonlinear interactions are more important. This hybrid model achieves the highest accuracy (0.6565) and ROC-AUC (0.7037), with an F1-score of 0.5768 and recall of 0.5824. However, it does not outperform XGBoost or LightGBM in recall or F1-score, indicating that while it improves overall stability and balance, it does not maximize sensitivity to severe crashes.

Cross-validation results further confirm model stability. LightGBM and XGBoost consistently perform best across folds with ROC-AUC values around 0.70 and low variance, indicating strong generalization. Logistic Regression maintains higher recall but shows less stability in precision, while Random Forest consistently underperforms relative to other models.

Across all models, feature importance analysis highlights consistent predictors of collision severity, including time of day (particularly rush hours), geographic location (ZIP code and borough), number of vehicles involved, vehicle type (such as SUVs, motorcycles, and E-bikes), and contributing behavioral factors such as distracted driving, failure to yield, and improper lane use. These patterns are consistent across both linear and tree-based models, reinforcing their predictive relevance.

Overall, no single model dominates across all metrics. Logistic Regression provides the strongest recall and is most suitable for maximizing coverage of severe crashes, while gradient boosting models provide the strongest overall predictive performance. The hybrid model offers a balanced compromise with strong ROC-AUC but does not outperform boosting methods in identifying severe crashes. For the EMS use case, this suggests a trade-off between recall-focused linear approaches and more globally accurate nonlinear models depending on operational priorities.

Cross Validation Results 
<img width="1071" height="150" alt="Screenshot 2026-05-04 at 6 40 52 PM" src="https://github.com/user-attachments/assets/db9d616d-e57e-41f0-8af1-9f9fa24370d2" />

Test Results 
<img width="553" height="97" alt="Screenshot 2026-05-04 at 6 41 04 PM" src="https://github.com/user-attachments/assets/959beb9b-90b4-46f3-a387-91901bcf7184" />

Hybrid Model Cross Validation
<img width="278" height="103" alt="Screenshot 2026-05-04 at 6 41 25 PM" src="https://github.com/user-attachments/assets/799ff167-3959-49af-b1f6-0b5db514221a" />

<img width="797" height="441" alt="Screenshot 2026-05-04 at 6 42 11 PM" src="https://github.com/user-attachments/assets/8e2a518f-03fd-4221-9e08-0c78920dce0d" />


## Discussion

Overall, we partially achieve our primary goal of building a predictive system to identify NYC traffic collisions that result in injury or fatality, with the intent of supporting EMS resource allocation. The models successfully learn meaningful patterns from temporal, spatial, and behavioral features, and all approaches significantly outperform a naive baseline. In particular, gradient boosting models and the hybrid system demonstrate that collision severity is strongly influenced by nonlinear interactions between time of day, location, vehicle type, and driver behavior.

From a stakeholder perspective, the system provides useful predictive signals that could help EMS anticipate higher-risk periods and locations. The Logistic Regression model, in particular, aligns well with EMS priorities due to its high recall, meaning it captures a large proportion of severe crashes. However, this comes at the cost of false positives, which could lead to inefficient resource allocation. On the other hand, XGBoost, LightGBM, and the hybrid model offer better overall discrimination but reduce recall, highlighting a direct trade-off between operational coverage and prediction precision.

While the models are not yet suitable for real-time deployment, they demonstrate clear potential for supporting EMS decision-making through risk scoring, shift planning, and hotspot identification. The strongest contribution of this work is not a single optimal model, but rather a comparative framework showing how different modeling strategies align differently with EMS operational needs.

## Limitations

Despite promising results, several limitations affect the robustness and practical deployment of this system. First, the dataset contains a significant amount of missing or incomplete spatial information, with a large portion of records lacking borough or precise coordinate data. Although we applied imputation strategies using intersection-level matching and ZIP centroids, these approximations introduce noise and may reduce spatial accuracy.

Second, the target formulation simplifies severity into a binary classification (injury/fatal vs. property damage). While this improves model stability, it collapses important distinctions between injury severity levels and fatal crashes, which are operationally very different for EMS response prioritization. The extreme rarity of fatal crashes (0.27%) also limits the model’s ability to learn meaningful patterns specific to fatalities.

Third, while temporal splitting improves realism, the models still rely on historical patterns that may not fully capture evolving traffic conditions, policy changes, or external disruptions such as weather or construction. Additionally, some features (e.g., contributing factors) are derived from post-incident reporting and may not always be immediately available in real-time prediction settings, limiting deployment feasibility.

Finally, although the hybrid model introduces a more flexible decision mechanism, its rule-based gating strategy is heuristic rather than learned, which may limit optimal performance compared to a fully trained stacking or meta-learning approach.

## Future Work

Future improvements include incorporating real-time external data such as weather conditions to better capture environmental factors affecting crash risk. Additional work could also focus on extending the model to multi-class severity prediction and replacing the heuristic hybrid approach with a learned stacking model for improved performance and stability.
