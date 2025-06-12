# Pipeline Integrity Monitoring with AI and Multi-Modal Data Fusion

## Project Overview

This project focuses on developing an advanced predictive model for pipeline integrity, specifically targeting the early detection of corrosion, leaks, and structural failures. The core innovation lies in integrating diverse data sources – traditional SCADA data, geospatial information (pipeline geometry), and future external data streams from satellite imagery and drone inspections – to provide a holistic view of pipeline health.

**Initial Project Goals & Vision:**
* **Goal:** Create an advanced predictive model for pipeline corrosion, leaks, and structural failures by integrating diverse data sources: SCADA data (pressure, temperature, flow), historical inspection reports, environmental data (soil type, weather), and new data streams from satellite imagery (thermal anomalies, vegetation stress) and drone inspections (visual defects).
* **Techniques:** Geospatial analytics, image recognition (CNNs) for anomaly detection in imagery, fusion of heterogeneous data types, graph neural networks to model pipeline networks and dependencies, probabilistic risk assessment.
* **Impact:** Prevent catastrophic failures, reduce environmental impact, optimize maintenance schedules, and ensure regulatory compliance.

## Pipeline Infrastructure Overview

The project utilizes data simulated for a pipeline segment with the following characteristics:
* **Pipeline Start:** (40.8, 22.5)
* **Pipeline End:** (40.7, 23.5)
* **Total Length (UTM):** Approximately 85.18 kilometers
* **Pipeline Segments:** Generated 86 segments, each roughly 1 kilometer in length, for granular analysis.
* **Ancillary Points:** 5 critical points (e.g., pump stations, valves) were also defined along the pipeline.

## Project Journey & Key Phases

### Phase 1: Initial Data Exploration & SCADA-based Modeling (Challenges)

Our journey began with a dataset of raw SCADA (Supervisory Control and Data Acquisition) sensor readings (`pressure_psi`, `temperature_c`, `flow_rate_m3_per_hr`, `vibration_m_per_s2`) spanning from September 2024 to June 2025, recorded every 15 minutes. The objective was to predict an `alarm_flag` (binary: 1 for alarm).

**Key Challenges Encountered:**
1.  **Extreme Class Imbalance:** Out of over 3 million data points, only 760 instances (across 591 unique timestamps) were actual alarms. This severe imbalance made it difficult for models to learn.
2.  **Lack of Discriminative Features:** Even after extensive feature engineering from SCADA data (rolling statistics, rates of change, EWMA, interaction terms), our initial LightGBM model struggled. The predicted probability distributions for alarms and non-alarms showed very little separation, rendering effective alarm thresholding impossible.

### Phase 2: Breakthrough with Synthetic External Data (Proof-of-Concept)

Recognizing the limitations of SCADA data alone for early detection, we hypothesized that direct external indicators would be crucial. To rapidly validate this, we integrated **synthetic features** simulating data from drone/satellite imagery:
* `pipeline_displacement_mm`: Simulating ground movement/structural shift.
* `leak_detection_score`: Simulating direct leak indicators.
* `vegetation_stress_index`: Simulating environmental impact, often linked to leaks.

These synthetic features were engineered to be highly correlated with alarm conditions, providing a clear signal.

**Results of Synthetic Data Integration:**
The impact was dramatic:
* **Perfect Recall (100%):** The model successfully identified every single alarm in both validation and test sets.
* **High Precision (86-94%):** Significantly reduced false alarms.
* **Perfect ROC AUC (1.0):** Indicating outstanding discriminatory power.
* **Clear Probability Separation:** The model's predicted probabilities for alarms and non-alarms became distinct and easily separable, enabling reliable threshold setting.

This phase served as a powerful **proof-of-concept**, demonstrating the immense potential of incorporating external, direct physical/environmental data for robust pipeline anomaly detection.

## Impact & Real-World Implications

The success with synthetic features validates the approach for a real-world deployment:
* **Enhanced Accuracy:** Achieve high recall (critical for safety) and high precision (reducing operational costs).
* **Proactive Maintenance:** Shift from reactive alarm response to predictive intervention, addressing issues before they escalate.
* **Improved Safety & Environmental Protection:** Early detection minimizes damage and risk.
* **Optimized Operations:** More efficient, targeted inspections and reduced unscheduled downtime.

## Plan for Real Data Integration (The Ultimate Goal)

The immediate next step, if this were a real-world project, would be to focus on how to actually acquire, process, and integrate real drone/satellite imagery and other external data sources to generate these features. This involves:

1.  **Data Acquisition Strategy:**
    * **Sensors:** LiDAR (for displacement), Thermal cameras (for leaks), Hyperspectral/Multispectral cameras (for vegetation stress, specific gas signatures), High-resolution optical cameras.
    * **Platforms:** Hybrid approach combining satellite monitoring (broad coverage, routine InSAR) and drone inspections (high-resolution, on-demand, targeted follow-up).
    * **Frequency:** Adaptive, from monthly/quarterly (satellite) to on-demand (drones for specific events/suspect areas).

2.  **Image Processing & Feature Extraction Pipeline:**
    * Developing robust computer vision and GIS pipelines to transform raw imagery into quantitative features:
        * Displacement mapping (e.g., from LiDAR point clouds or InSAR interferograms).
        * Leak signature detection (e.g., thermal anomalies, spectral unmixing for hydrocarbons).
        * Vegetation health analysis (e.g., NDVI calculations, anomaly detection).
    * Aggregation of spatially high-resolution image data to match pipeline segments or SCADA sensor locations.

3.  **Data Alignment & Fusion:**
    * **Spatial Alignment:** Precise georeferencing and spatial joining of image-derived features with pipeline GIS data.
    * **Temporal Alignment:** Strategies to fuse high-frequency SCADA data with less frequent imagery (e.g., Last Observation Carried Forward, intelligent interpolation, event-based triggers).
    * **Unified Data Platform:** Building scalable data infrastructure (e.g., data lake) for heterogeneous data types.

## Repository Structure

pipeline_integrity_project/
├── .gitignore
├── README.md
├── LICENSE
├── requirements.txt
├── data/
│   ├── raw/                 # Raw input data (e.g., synthetic SCADA, geojson)
│   ├── processed/           # Intermediate processed data
│   └── features/            # Feature-engineered datasets (including synthetic external features)
├── notebooks/               # Jupyter notebooks for exploration, analysis, and modeling
│   ├── 01_data_understanding_and_initial_eda.ipynb
│   ├── 02_feature_engineering_and_preprocessing.ipynb
│   ├── 03_model_development_and_evaluation.ipynb
│   └── 04_synthetic_feature_impact_analysis.ipynb
├── src/                     # Python source code for reusable functions (e.g., data processing, model utilities)
│   ├── data_preparation.py
│   ├── feature_engineering.py
│   ├── model_training.py
│   └── utils.py
├── models/                  # Directory for trained model artifacts
├── config/                  # Configuration files (e.g., model hyperparameters, file paths)
└── reports/
├── figures/             # Directory to store key plots and visuals
└── final_report.md      # Summary of project findings and conclusions


## Author 

* Emmanouil Amygdalas/Falcon Consulting Professionals

## License

This project is licensed under the [Choose an appropriate license.