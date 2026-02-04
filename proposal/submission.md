# Predicting Traffic Collision Risk in New York City

### Team:

Adelina Dunina (login: Adelina2302) POC, name, name

### Introduction

Traffic collisions represent a persistent urban safety challenge, with New York City experiencing thousands of crashes annually that result in injuries, fatalities, and significant economic costs. While city officials have implemented various safety interventions, the strategic allocation of these resources remains difficult without accurate predictive models. This project aims to develop machine learning models that can predict where and when traffic collisions are most likely to occur in New York City, enabling data-driven decisions about infrastructure improvements and resource deployment.

Our approach differs from existing collision analysis methods in several key ways. Rather than simply identifying historical hotspots, we will build predictive models that combine multiple data sources to forecast future collision risk. Specifically, we will integrate temporal patterns (time of day, day of week, seasonal variation), spatial features (geographic location, street network characteristics), and environmental factors (weather conditions, road surface conditions) into a unified predictive framework. We will evaluate multiple machine learning methods including Logistic Regression, Random Forest, and XGBoost to determine which techniques best capture the complex patterns underlying collision occurrence.

The practical applications of this work are substantial and span multiple stakeholder groups:
-	**City traffic planners** can use our risk predictions to prioritize locations for safety improvements such as traffic signals, protected crosswalks, and speed cameras
-	**Emergency medical services** can optimize resource allocation by positioning personnel in high-risk areas during predicted peak danger periods
-	**Navigation applications** could use our models to route vulnerable road users (pedestrians and cyclists) away from the most dangerous intersections
-	**Policy makers** can use the models to establish baselines and evaluate whether implemented safety interventions are achieving their intended effects
By providing actionable predictions rather than descriptive statistics, this project has the potential to meaningfully improve traffic safety outcomes in New York City.

### Literature Review

Current collision analysis typically relies on descriptive statistics and visualization to identify accident-prone locations [1]. Many cities employ GIS-based hotspot analysis that examines historical crash density to pinpoint dangerous areas, but these methods are purely retrospective and cannot predict future collision risk [2]. While useful for understanding past patterns, this approach provides limited guidance for proactive safety interventions.

Recent research has begun applying machine learning techniques to collision prediction. Spatial clustering methods such as K-means and DBSCAN have been used to identify high-risk zones, but these approaches fail to account for temporal variation in collision patterns [3]. Conversely, time-series models like ARIMA can forecast collision counts over time but miss the crucial spatial dimension of where crashes occur [4]. Some studies have achieved promising results using tree-based methods like Random Forest and XGBoost for predicting collision severity, demonstrating that ensemble approaches can effectively capture the complex factors contributing to crashes [5].

The main limitation of current practice is the separation of spatial and temporal analysis. Few studies have successfully integrated detailed location data with time-based patterns and contributing factors such as weather or road conditions into a unified predictive framework. Additionally, most existing work focuses on explaining past collisions rather than providing actionable predictions for future risk.

### Stakeholder Needs

Our project addresses the needs of multiple stakeholder groups, each with specific requirements for how collision risk predictions should be structured and delivered.

**City Traffic Planners** are responsible for allocating limited budgets across competing safety infrastructure needs. They need predictions at intersection-level granularity that identify which specific locations warrant investment in traffic signals, protected crosswalks, or speed cameras. Because infrastructure improvements are expensive and permanent, planners require high-precision predictions—false positives that recommend costly interventions at genuinely safe locations waste scarce public resources. They also need predictions broken down by time of day and day of week, as some intersections may only be dangerous during rush hours or weekends, which affects the type of intervention needed.

**Emergency Medical Services** must strategically position ambulances and medical personnel across the city to minimize response times to serious collisions. Unlike fixed infrastructure, EMS resources can be dynamically deployed, so they need predictions that update frequently (ideally hourly or by shift) and highlight when and where severe injury crashes are most likely to occur. Their primary concern is recall over precision—failing to position resources near an actual severe crash has life-or-death consequences, while occasionally over-deploying to a quiet area is an acceptable cost. They need predictions specifically for crashes likely to result in serious injuries, not just collision occurrence generally.

**Navigation Applications** (such as Google Maps, Waze, and cycling apps) could integrate collision risk predictions to route vulnerable road users away from the most dangerous intersections and street segments. These applications need risk scores that can be incorporated into routing algorithms, ideally updated in real-time or at least daily. Because they serve millions of users making individual routing decisions, the predictions must be granular (street-segment level) and distinguish between risk to different road user types—an intersection dangerous for cyclists may be relatively safe for cars. The predictions should also account for time of day, as commuters need safe routes during peak hours.

**Policy Makers** at the city and state level need to justify safety interventions to the public and evaluate whether implemented measures are achieving their intended effects. They require models that can establish baseline risk predictions before an intervention (e.g., "this intersection was predicted to have 15 crashes per year") and then compare actual outcomes after implementation ("only 8 crashes occurred after installing the traffic signal"). This requires models that are interpretable enough to explain to non-technical audiences and stable enough to make valid before-after comparisons. Policy makers also need predictions stratified by borough and neighborhood to ensure safety investments are distributed equitably across the city.

These diverse stakeholder needs inform our modeling approach and evaluation strategy. We will prioritize models that provide location-specific, time-varying predictions with sufficient interpretability to guide infrastructure decisions, while also achieving the precision and recall balance appropriate for resource allocation.

### Data and Methods

### Data

**Primary Dataset:** NYC Motor Vehicle Collisions - Crashes

- **Source**: NYC Open Data Portal (https://data.cityofnewyork.us/Public-Safety/Motor-Vehicle-Collisions-Crashes/h9gi-nx95/about_data)
- **Size**: 2+ million records from 2012 to present
- **Features**: 29 columns including:
- **Temporal**: crash date, crash time
- **Spatial**: borough, zip code, latitude/longitude, street names
- **Impact**: injuries and deaths by road user type (pedestrians, cyclists, motorists)
- **Causes**: contributing factors (up to 5 vehicles)
- **Vehicle types**: up to 5 vehicles involved

**Data Reliability**: This is official NYPD data, collected from police reports for all collisions. It's well-maintained with regular updates and includes metadata explaining each field.

*Additional Datasets (if needed)*

- NYC Weather Archive for road conditions
- Street network data from OpenStreetMap for intersection characteristics
- Traffic volume data from NYC DOT sensors

### Methods

Our analysis will require substantial data preprocessing to address quality issues. We will handle missing values in location coordinates and contributing factors, geocoding crashes using street names where coordinates are unavailable. We will extract temporal features (hour, day of week, month, season) and spatial features (borough, neighborhood characteristics, intersection proximity). Categorical variables will be encoded for modeling, and we will address the severe class imbalance inherent in the dataset.

We will evaluate multiple machine learning approaches in two phases. First, we will establish baseline performance using Logistic Regression for collision severity prediction and Poisson Regression for collision counts. Second, we will implement tree-based ensemble methods (Random Forest, XGBoost, LightGBM) to capture non-linear patterns and complex feature interactions.

Our evaluation approach aligns with stakeholder needs for actionable predictions. We will use time-based cross-validation, training on earlier periods and testing on later periods. We will employ multiple metrics: Precision and Recall for identifying high-risk locations (critical for traffic engineers), F1-score for balanced performance, Mean Absolute Error for collision counts, and Top-K accuracy to measure how often actual collisions occur in our highest-predicted risk areas. Model performance will be assessed across different boroughs and time periods to ensure generalizability.

### Project Plan

| Period | Activity | Milestone |
|--------|----------|-----------|
| 2/3 - 2/16 | Data exploration and cleaning. Initial stakeholder analysis. EDA on collision patterns and spatial distribution. | Clean dataset ready. Stakeholder needs documented. Data quality issues identified. |
| 2/17 - 3/2 | Feature engineering (temporal, spatial features). Train baseline models (Logistic Regression, Poisson Regression). | Baseline model performance established. Feature importance analysis complete. |
| 3/3 - 3/16 | Implement tree-based models (Random Forest, XGBoost, LightGBM). Hyperparameter tuning and cross-validation. | Best performing traditional ML model identified. Model comparison complete. |
| 3/17 - 3/30 | Advanced model refinement. Address class imbalance with SMOTE and resampling techniques. Performance optimization. | Final model selected. Performance metrics documented and validated. |
| 3/31 - 4/13 | Build visualization dashboard for predictions. Create risk heatmaps. Prepare final report and presentation. | Deliverables complete. Project documentation finalized. |

### Risks

We anticipate the following risks to the project and have identified mitigation strategies for each:

**Risk 1: Data Quality and Missing Values**

**Problem:** The NYC collisions dataset contains substantial missing information,particularly in location coordinates, contributing factors, and vehicle details. Many records list contributing factors as "Unspecified" or "Unknown." This missing data may not be random—certain types of collisions or specific boroughs may be systematically underreported, which could bias our models.

**Mitigation:** We will conduct thorough analysis of missing data patterns to understand if missingness is systematic. Records with excessive missing values will be excluded from analysis. Where possible, we will use street names to geocode missing coordinates. We will test our models with different approaches to handling missing data to ensure results are robust.

**Risk 2: Class Imbalance**

**Problem:** Severe and fatal collisions are rare compared to minor crashes. Standard machine learning approaches may simply predict "no severe injury" for all cases, achieving high accuracy while failing to identify the high-risk scenarios that matter most to stakeholders.

**Mitigation:** We will employ resampling techniques (SMOTE, ADASYN) and cost-sensitive learning to address imbalance. Additionally, we will frame the problem as a risk-scoring or ranking task rather than binary classification, which may be more appropriate for identifying high-risk locations and times.

**Risk 3: Spatial and Temporal Reporting Bias**

**Problem:** Collision reporting practices vary across NYC boroughs and neighborhoods. Wealthier areas may have more complete reporting, while certain precincts may have different standards for documenting minor collisions. These variations could cause our models to reflect reporting patterns rather than actual collision risk.

**Mitigation:** We will analyze patterns separately by borough and time period to assess consistency. Model performance will be evaluated across different geographic areas to identify potential bias. We will examine which features the models rely on most heavily to ensure they capture genuine risk factors rather than reporting artifacts.

### References

[1] Chen, Q., et al. "Road traffic safety analysis based on a GIS-based system." *International Journal of Transportation* 8.2 (2020): 45-58.

[2] Plug, C., et al. "Spatial and temporal visualisation techniques for crash analysis." *Accident Analysis & Prevention* 43.6 (2011): 1937-1946.

[3] Anderson, T. K. "Kernel density estimation and K-means clustering to profile road accident hotspots." *Accident Analysis & Prevention* 41.3 (2009): 359-364.

[4] Wang, L., et al. "Short-term traffic accident prediction based on ARIMA." *Transportation Research Record* 2672.38 (2018): 86-97.

[5] Santos, D., et al. "Predicting crash injury severity with machine learning: A comparative study." *Journal of Safety Research* 78 (2021): 207-221.