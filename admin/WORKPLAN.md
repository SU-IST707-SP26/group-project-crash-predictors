# WORKPLAN.md

## Active Tasks

### Milestone 1: Data Acquisition & Exploration (Week 1-2)
- [✅] M1.T1 — Download NYC Motor Vehicle Collisions dataset from Open Data Portal (Adelina)
- [✅] M1.T2 — Initial exploratory data analysis on collision patterns (Adelina)
- [✅] M1.T3 — Analyze data quality issues (missing coordinates, null values) (Adelina)
- [❌] M1.T4 — Document dataset structure and column meanings (Adelina)
- [❌] M1.T5 — Identify stakeholder needs and success criteria (Adelina)

### Milestone 2: Data Cleaning & Preparation (Week 1-2)
- [ ] M2.T1 — Handle missing latitude/longitude values (TBD)
- [ ] M2.T2 — Geocode crashes using street names where coordinates missing (TBD)
- [ ] M2.T3 — Clean contributing factor fields (handle "Unspecified") (TBD)
- [ ] M2.T4 — Filter dataset to recent years (2022-present) (TBD)
- [ ] M2.T5 — Create analysis-ready dataset with clean features (TBD)

### Milestone 3: Feature Engineering (Week 3-4)
- [ ] M3.T1 — Extract temporal features (hour, day of week, month, season) (TBD)
- [ ] M3.T2 — Create spatial features (borough indicators, zip code groups) (TBD)
- [ ] M3.T3 — Encode categorical variables (vehicle types, contributing factors) (TBD)
- [ ] M3.T4 — Engineer target variable (severity classification or count) (TBD)
- [ ] M3.T5 — Create train/test split using temporal cutoff (TBD)

### Milestone 4: Baseline Modeling (Week 3-4)
- [ ] M4.T1 — Implement Logistic Regression for severity prediction (TBD)
- [ ] M4.T2 — Implement Poisson Regression for collision counts (TBD)
- [ ] M4.T3 — Establish baseline evaluation metrics (Precision, Recall, F1) (TBD)
- [ ] M4.T4 — Analyze feature importance from baseline models (TBD)

### Milestone 5: Tree-Based Models (Week 5-6)
- [ ] M5.T1 — Implement Random Forest classifier (TBD)
- [ ] M5.T2 — Implement XGBoost model (TBD)
- [ ] M5.T3 — Implement LightGBM model (TBD)
- [ ] M5.T4 — Hyperparameter tuning using grid search (TBD)
- [ ] M5.T5 — Compare model performance against baseline (TBD)

### Milestone 6: Model Refinement & Validation (Week 7-8)
- [ ] M6.T1 — Address class imbalance using SMOTE or resampling (TBD)
- [ ] M6.T2 — Implement cross-validation for model stability (TBD)
- [ ] M6.T3 — Feature selection and engineering refinement (TBD)
- [ ] M6.T4 — Final model selection based on stakeholder needs (Team)

### Milestone 7: Visualization & Deliverables (Week 9-10)
- [ ] M7.T1 — Create risk heatmaps by location and time (TBD)
- [ ] M7.T2 — Build visualization dashboard for predictions (TBD)
- [ ] M7.T3 — Prepare final report and documentation (Team)
- [ ] M7.T4 — Create presentation slides (Team)

---

## Changelog

### 2026-02-08
- (Adelina) ❌ M1.T4 — NYC Open Data Portal already provides comprehensive data dictionary
- (Adelina) ❌ M1.T5 — removed — Stakeholder needs already defined in proposal document

### 2026-02-03
- Defined 7 milestones aligned with 10-week timeline
- Tasks to be assigned to team members once roles determined