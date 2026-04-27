## Team Name - Risk Masters  

# Daily increments (notebooks) 

Member 1  → day_01_exploration.ipynb, day_02_ingestion.ipynb, day_03_database.ipynb, day_05_checkpoint.ipynb

Member 2  → day_04_cleaning_features.ipynb, day_07_statistics.ipynb

Member 3  → day_08_modeling.ipynb 

Member 4  → day_06_eda.ipynb


# Sources 

member 1; config.py, ingestion.py, cleaning.py, pipeline.py

member 2; features.py, 

member 3; quality.checks.py

member 4; reports.py 

# Tables

Member 1; raw_cotton, raw_weather, clean_cotton, clean_weather

Member 2; features, features_with_risk


## 1. Problem Definition

Cotton production in Azerbaijan is a vital economic sector, yet it remains highly vulnerable to unpredictable weather patterns and localized environmental stressors. Traditional planning often relies on historical averages, which fail to account for the specific risks associated with different growth stages (planting, flowering, harvesting).

### What we built:

A machine learning system that predicts **cotton yield (in tonnes) for 29 districts of Azerbaijan for the year 2025 and 2026**, and also estimates **how risky each growth stage of the cotton season will be**. 

### Targets;

Predict cotton yield (tonnes) for 29 districts of Azerbaijan for 2025–2026
Estimate risk levels for each growth stage of the cotton season 

## Features

Daily weather metrics (max/min/mean temp, precipitation, humidity, wind speed, ET0) and historical yield trends.


## Prediction Horizon

Seasonal/Yearly (Forecasting for 2025–2026).


### Why it matters 

This matters because it replaces guesswork with data-driven planning for cotton production. By predicting yield across districts, stakeholders can better plan supply, logistics, and exports. The risk estimates highlight vulnerable growth stages, allowing early preventive actions. Overall, it helps reduce losses and improve decision-making. 

## 2. Data Sources

Source 1 — Cotton Production Dataset  

- 29 districts × 25 years (2000–2024) = 725 observations
- Values are annual cotton yield in tonnes per district
- Had 191 missing values and we deleted all districts with at least one missing value, and there are 15 districts left. 


Source 2 — Open-Meteo Historical Weather API 

- We pulled daily weather for 5 weather station locations across Azerbaijan: Ganja, Sabirabad, Lankaran, Shamkir, Nakhchivan
- Variables collected: max/min/mean temperature, precipitation, humidity, wind speed, evapotranspiration (ET0)
- 25 years × 365 days × 5 locations = ~45,000 daily rows 





## 👥 Team: Risk Masters – Roles & Responsibilities

The project was divided into four specialized streams to ensure a robust end-to-end data pipeline, from raw API ingestion to predictive modeling and visual reporting.

### **Esli Ehmedova | Data Engineer**
* **Role:** Lead Architect for the "Raw to Clean" pipeline and project infrastructure.
* **Core Tasks:**
    * **Project Setup:** Established the directory structure and environment requirements.
    * **Data Collection:** Automated the retrieval of 24 years of historical weather data via APIs for strategic regions (Ganja, Shamkir, Sabirabad, etc.).
    * **Data Reshaping:** Transformed the cotton production dataset from wide format (years as columns) to long format (tidy data) for machine learning readiness.
    * **Data Cleaning:** Managed missing value imputation, outlier detection, and temporal alignment between weather and yield datasets.
* **Key Files:** `config.py`, `ingestion.py`, `cleaning.py`, `pipeline.py`

### **Narmin Dirayeva | Agro-Analyst & Feature Engineer**
* **Role:** Technical lead for translating agricultural science into mathematical features.
* **Core Tasks:**
    * **Growth Stages:** Defined specific windows for Planting, Vegetative, Boll Forming, and Harvest stages.
    * **GDD Calculation:** Implemented Growing Degree Days (GDD) formulas specific to cotton biology (Base 15.5°C).
    * **Advanced Features:** Engineered specialized metrics including heat stress days, frost days, and dry streaks per growth stage.
    * **Risk Logic:** Developed the mathematical logic and thresholds for risk labels (binary) and risk percentages (0-100%).
* **Key Files:** `features.py`

### **Khaver Gasimova | Machine Learning Engineer**
* **Role:** Lead for training, tuning, and evaluating predictive performance.
* **Core Tasks:**
    * **Risk Modeling:** Developed and trained four separate models to assess risk for each individual growth stage.
    * **Yield Prediction:** Built the primary predictive engine (XGBoost/LightGBM) to forecast annual tonnage.
    * **Feature Integration:** Integrated the risk scores from stage-specific models as meta-features for the final yield model.
    * **Evaluation:** Generated comprehensive metrics (RMSE, MAE, Accuracy) and managed model serialization via `joblib`.
* **Key Files:** `quality.checks.py`, `model_registry/`

### **Ulviyya Aliyeva | Data Analyst & Visualizer**
* **Role:** Lead for pattern discovery and stakeholder communication.
* **Core Tasks:**
    * **Exploratory Data Analysis (EDA):** Created deep-dive visualizations including correlation heatmaps and scatter plots (e.g., GDD vs. Yield impact).
    * **Regional Comparisons:** Analyzed and visualized yield variance and averages across different regions of Azerbaijan.
    * **Forecast Reporting:** Designed the output interface and dashboards for 2025–2026 predictions.
    * **Documentation:** Compiled the final methodology, project limitations, and future scalability roadmap.
* **Key Files:** `reports.py`

---

## 📅 Daily Activities (Technical Workflow)

Our team followed a sequential "Daily Increment" sprint to build the system from the ground up:

| Day | Phase | Activity & Ownership |
| :--- | :--- | :--- |
| **Day 01-02** | **Exploration & Ingestion** | Initial API connection tests and raw CSV sourcing (Esli). |
| **Day 03** | **Data Architecture** | SQL/Database schema setup and dataset reshaping (Esli). |
| **Day 04** | **Agro-Feature Engineering** | Calculating GDD and defining growth stage windows (Narmin). |
| **Day 05** | **Data Checkpoint** | Validating cleaned data and merging weather/yield tables (Esli). |
| **Day 06** | **EDA & Insights** | Visualizing correlations and regional yield trends (Ulviyya). |
| **Day 07** | **Statistical Logic** | finalizing Risk Percentage logic and stage-based labeling (Narmin). |
| **Day 08** | **ML Modeling** | Training XGBoost models and evaluating error metrics (Khaver). |
