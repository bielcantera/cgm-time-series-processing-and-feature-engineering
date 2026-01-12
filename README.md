# CGM + Insulin/Carbs Time-Series Processing (Feature Engineering)

## Overview
This project builds a clean, analysis-ready time series dataset by combining continuous glucose monitoring (CGM) readings with treatment/event data (insulin, carbs, and basal insulin settings).

The notebook focuses on **data quality**, **time alignment**, **gap handling**, and **feature engineering** to produce a consistent **5-minute interval dataset** suitable for downstream modeling or analytics.

---

## Problem & Goal
Real-world CGM and treatment datasets are typically messy:
- timestamps are not aligned across sources
- missing values and inconsistent time intervals are common
- treatment events need to be translated into time-dependent signals

**Goal:** create a single dataset where each 5-minute timestamp includes:
- glucose measurements (cleaned)
- engineered insulin exposure over time
- engineered carb exposure over time
- basal + bolus insulin combined features

---

## Data

The datasets included in this repository are **fully anonymized and protected**.
All personal identifiers have been removed, and the data cannot be traced back to any individual.

The data is provided exclusively for educational, analytical, and portfolio demonstration purposes.
The data does not contain personal identifiers, names, or sensitive metadata beyond anonymized time-series measurements.

---

## Key Processing Steps

### 1) Loading & Inspection
- dataset shape, missing values, and datatype checks
- validation of time ranges and overlap between sources

### 2) Timestamp Standardization & Filtering
- timestamps are converted to proper datetime types
- data is filtered to a common time window to ensure consistent merging

### 3) Event Alignment (CGM + Treatments)
Treatments are merged into the CGM timeline using time-aware merging:
- each CGM record receives the most recent prior treatment event
- glucose is consolidated into a single signal: `glucose_combined`

### 4) Outlier Handling
The pipeline removes extreme values likely caused by measurement/logging errors:
- insulin values above a high threshold are removed
- unrealistic glucose values are removed

### 5) Gap Filling & Interpolation (Time Series)
- gaps larger than 5 minutes are detected
- missing 5-minute rows are inserted
- numerical signals are interpolated:
  - `glucose_combined`: linear interpolation + forward/back fill
  - `insulin`: linear interpolation + forward/back fill
  - `carbs`: assumed 0 during gaps (no recorded intake)

### 6) 5-Minute Feature Engineering (Core Output)
The project creates time-dependent exposure features:
- **Bolus insulin exposure** distributed across **60 × 5-min intervals** (5 hours)
- **Basal insulin exposure** computed using a daily assumption and adjusted by `percent` and `duration`
- **Carb exposure** distributed across **60 × 5-min intervals** (5 hours)

Final engineered features include:
- `insulin_5min`
- `basal_insulin_5min`
- `insulin_total_5min`
- `carbs_5min`

---

## Output
The notebook exports a cleaned dataset:
- `cleaned_data.csv`

This dataset is structured for:
- predictive modeling (e.g., glucose forecasting / classification)
- event impact analysis (carbs/insulin effects)
- time series visualization and monitoring

---

## Assumptions (Important)
To convert events into time-dependent signals, the notebook uses explicit assumptions:
- daily basal insulin is approximated (e.g., **40 units/day**)
- bolus insulin effects are distributed over **5 hours**
- carb effects are distributed over **5 hours**
- carbs during missing intervals are treated as **0** unless recorded

These assumptions should be adjusted depending on the clinical context and dataset.

---

## Tech Stack
- Python
- pandas, numpy
- matplotlib

---

## How to Run
1. Clone the repository.
2. Ensure the data files are present in the `data/` folder.
3. Open `cgm_time_series_feature_engineering.ipynb`.
4. Run the notebook top-to-bottom.

---

## Key Findings & Conclusions
- Aligning event logs to sensor readings is essential to interpret treatment effects over time
- Handling time gaps (insertion + interpolation) is required for consistent time-based modeling
- Feature engineering transforms discrete events (insulin/carbs) into continuous exposure signals
- The resulting dataset is significantly more suitable for analysis than raw CGM/event logs

Detailed plots and intermediate checks are provided in the notebook.

---

## Author
Biel Cantera
Boris Ivanov
