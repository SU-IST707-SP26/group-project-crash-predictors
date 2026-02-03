# Predicting Traffic Collision Risk in New York City

### Team:

Adelina Dunina (login: Adelina2302), 

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


### Data and Methods

**Data**

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

**Methods**


### Project Plan

| Period | Activity | Milestone |
|--------|----------|-----------|
| 2/3 - 2/16 | Data exploration and cleaning. Initial stakeholder analysis. EDA on collision patterns and spatial distribution. | Clean dataset ready. Stakeholder needs documented. Data quality issues identified. |
| 2/17 - 3/2 | Feature engineering (temporal, spatial features). Train baseline models (Logistic Regression, Poisson Regression). | Baseline model performance established. Feature importance analysis complete. |
| 3/3 - 3/16 | Implement tree-based models (Random Forest, XGBoost, LightGBM). Hyperparameter tuning and cross-validation. | Best performing traditional ML model identified. Model comparison complete. |
| 3/17 - 3/30 | Advanced model refinement. Address class imbalance with SMOTE and resampling techniques. Performance optimization. | Final model selected. Performance metrics documented and validated. |
| 3/31 - 4/13 | Build visualization dashboard for predictions. Create risk heatmaps. Prepare final report and presentation. | Deliverables complete. Project documentation finalized. |

### Risks


### References