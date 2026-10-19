# ASTEROIDS
NASA NEO Hazard Classification Dashboard

An end-to-end planetary defense analytics suite and machine learning dashboard built using data from NASA's Jet Propulsion Laboratory (JPL) Center for Near-Earth Object Studies (CNEOS) and the Sentry Earth Impact Monitoring system.

---

## Project Overview

This project ingests, wrangles, models, and visualizes near-Earth asteroid trajectories and potential impact threats[cite: 2]. By combining orbital dynamics, historical/projected close-approach flybys, and automated classification algorithms, the dashboard provides domain analysts and non-technical stakeholders with actionable risk assessments[cite: 1, 2].

### Key Data Sources
* **CNEOS Close Approach Data (CAD):** Comprehensive log of all past and future close approaches to Earth (miss distance, time, and orbital velocity)[cite: 2].
* **JPL Sentry Risk Table:** Automated impact monitoring data quantifying potential impact probabilities, Palermo cumulative hazard scales, and Torino ratings[cite: 2].
* **Near Earth Object Web Service (NeoWs):** Physical properties of near-Earth objects including estimated diameter limits and absolute magnitude[cite: 2].

---

## Data Model & Architecture

The analytical pipeline follows a star/snowflake schema optimized for Power BI reporting and machine learning evaluation[cite: 2]:

### Core Entities
* **`dim_asteroid`**: Master dimension containing asteroid identifiers, orbital classes (Apollo, Aten, Amor, etc.), absolute magnitude ($H$), eccentricity, and diameter boundaries[cite: 2].
* **`fact_close_approach`**: Historical and scheduled flyby events with nominal and minimum miss distances in Astronomical Units (`dist_au`, `dist_min_au`) and Lunar Distances (`dist_ld`)[cite: 2].
* **`fact_sentry`**: High-risk monitoring parameters including `impact_probability`, Palermo Scale (`ps_cum`, `ps_max`), Torino scale (`ts_max`), and potential impact years[cite: 2].
* **`ml_*` tables**: Validation outputs, model test evaluation metrics, cross-validation runs, and Gini/permutation feature importance scores across distinct feature sets[cite: 2].

---

## Dashboard Structure (Power BI)

The dashboard is structured into four functional pages designed with a dark command-center aesthetic[cite: 1]:

### 1. Overview (Catalog & Risk Summary)
* **KPI Metrics:** Total catalog size, total recorded close approaches, potentially hazardous asteroid (PHA) count, and overall PHA baseline rate.
* **Risk Heatmap:** Matrix cross-referencing `orbit_class` against `size_class` shaded by hazard concentration.
* **Orbit Treemap:** Relative volume distribution of asteroid orbit families.
* **Top 10 Threats:** Filtered ranking of prominent approaching asteroids.

### 2. Close Approaches (Trajectory Analysis)
* **Interactive Year Range Slicer:** Dynamic `Between` slider driving multi-decade approach timelines.
* **Proximity Distribution:** Clustered bar charts tracking close encounters by decade and hazard flag[cite: 2].
* **Trajectory Scatter Plot:** Miss distance in Lunar Distances (`dist_ld`) plotted against asteroid diameter (`diameter_mean_km`)[cite: 2].
* **Closest Passes Log:** Chronological table of tight-proximity flybys sorted ascending by Lunar Distance[cite: 2].

### 3. Asteroid Dossier (Drill-Through Target)
* **Dynamic Target Page:** Right-click drill-through activation from any table row across the report.
* **Physical & Orbital Profile:** Mass, diameter boundaries, magnitude, and orbital eccentricity[cite: 2].
* **Sentry Risk Assessment:** Potential impact probability, Torino rating, and Palermo hazard scales for flagged bodies[cite: 2].
* **Object Timeline:** Chronological log of past and future close-approach trajectories specific to the selected object[cite: 2].

### 4. ML Model Evaluation & Interpretability
* **Model Benchmark:** Side-by-side performance comparison of classifiers ($F_1$, Precision, Recall, PR-AUC, ROC-AUC) across test and cross-validation folds[cite: 2].
* **Confusion & Error Analysis:** Quantified evaluation of False Negatives (`FN`) vs. False Positives (`FP`)[cite: 2].
* **Feature Importance:** Gini and permutation importance rankings highlighting key physical predictors (e.g., diameter and absolute magnitude)[cite: 2].

---

## Key DAX Measures

// Overall Hazardous Percentage
PHA Rate = 
DIVIDE(
    CALCULATE(COUNTROWS(dim_asteroid), dim_asteroid[is_potentially_hazardous] = 1),
    COUNTROWS(dim_asteroid),
    0
)

// Minimum Miss Distance in Lunar Distances
Closest Miss Distance (LD) = 
MIN(fact_close_approach[dist_ld])

// Distinct Asteroids Approaching in Selected Timeframe
Asteroids Approaching = 
DISTINCTCOUNT(fact_close_approach[asteroid_id])

Getting Started
Prerequisites
Power BI Desktop (August 2023 release or newer recommended)

Python 3.9+ (if running the source ETL and model training scripts)

Required libraries: pandas, numpy, scikit-learn, requests

Opening the Report
Clone this repository:
git clone [https://github.com/your-username/nasa-asteroid-risk-monitor.git](https://github.com/your-username/nasa-asteroid-risk-monitor.git)

Navigate to the /dashboard directory.

Open Asteroid_Risk_Monitor.pbix in Power BI Desktop.

To refresh the data pipeline from updated local CSV exports, click Home → Refresh.
