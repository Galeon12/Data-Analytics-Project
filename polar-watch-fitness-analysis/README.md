# Polar Watch â€” Workout Vitals Analysis

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.8%2B-blue?logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/Statsmodels-Modeling-blue?logo=statsmodels&logoColor=white" alt="Statsmodels">
  <img src="https://img.shields.io/badge/Scipy-Statistics-blue" alt="Scipy">
  <img src="https://img.shields.io/badge/Matplotlib-Visualization-blue?logo=plotly&logoColor=white" alt="Matplotlib">
  <img src="https://img.shields.io/badge/Status-Completed-green" alt="Status">
</p>

<p align="center">
  <strong>A statistical analysis of workout data exported from a Polar watch</strong><br>
  Heart-rate distributions, caloric expenditure, session duration, and sport-type differences are examined, with a focus on comparing strength training against cardiovascular activity.
</p>

---

## ðŸ“‘ Table of Contents

- [About This Project](#about-this-project)
- [Problem Statement](#problem-statement)
- [Data Source](#data-source)
- [Project Structure](#project-structure)
- [Methodology](#methodology)
  - [1. Data Extraction](#1-data-extraction)
  - [2. Data Cleaning & Transformation](#2-data-cleaning--transformation)
  - [3. Exploratory Analysis](#3-exploratory-analysis)
  - [4. Statistical Modeling](#4-statistical-modeling)
- [Key Findings](#key-findings)
- [Technical Specifications](#technical-specifications)
- [Model Results](#model-results)
- [How to Run](#how-to-run)
- [Privacy Note](#privacy-note)
- [Author](#author)

---

## About This Project

This project analyzes personal workout data exported from a Polar watch via Polar Flow. The analysis covers 283 workouts over approximately one year, examining heart-rate distributions, caloric expenditure, session duration, and timing patterns. A regression model is built to predict calories burned, with diagnostic analysis to validate model assumptions.

The project demonstrates:
- **JSON data extraction** from nested device export formats
- **Statistical rigor** with OLS regression, linear mixed models, and diagnostic tests
- **Comparative analysis** between strength training and cardiovascular activities
- **Distribution fitting** and outlier detection

---

## Problem Statement

Fitness trackers generate rich time-series data, but extracting meaningful insights requires careful preprocessing and appropriate statistical models. This project addresses:

1. How do heart-rate distributions differ between strength training and cardiovascular sessions?
2. Can we build a reliable model to predict caloric expenditure from session characteristics?
3. What are the timing patterns of workouts (day of week, hour of day)?
4. How do session duration, heart rate, and activity type interact to determine calories burned?

---

## Data Source

Personal workout data exported from [Polar Flow](https://polar.flow.com). The export consists of JSON files (`training-session-*.json`) containing detailed session metrics.

> **Privacy:** The raw dataset is **not shared** in this repository. Users must export their own Polar Flow data to reproduce the analysis.

**Data characteristics:**
- **283 workouts** collected over approximately one year
- **6 activity types:** walking, strength_training, treadmill_running, cycling, running, and others
- **Rich metrics per workout:** heart-rate samples, calories, duration, distance, sport type, timestamps

---

## Project Structure

```
polar-watch-fitness-analysis/
â”œâ”€â”€ Polar.ipynb                      # Main analysis notebook
â”œâ”€â”€ Polar.pdf                        # PDF export of notebook
â”œâ”€â”€ mdl_results.txt                  # Model regression results
â””â”€â”€ img/
    â”œâ”€â”€ 99q_hr_vs_kilocalories_scatter_by_strength.png
    â”œâ”€â”€ duration_histogram.png
    â”œâ”€â”€ intensity_scatter.png
    â”œâ”€â”€ is_strength_vs_99q_hr_scatter.png
    â”œâ”€â”€ is_strength_vs_kilocalories_jitter.png
    â”œâ”€â”€ is_strength_vs_time_jitter.png
    â”œâ”€â”€ kilocalories_histogram.png
    â”œâ”€â”€ kilocalories_ts.png
    â”œâ”€â”€ mdl_predicted_vs_actual.png
    â”œâ”€â”€ mdl_qq.png
    â”œâ”€â”€ mdl_residuals.png
    â”œâ”€â”€ mdl_residuals_std.png
    â”œâ”€â”€ q99_hr_histogram.png
    â”œâ”€â”€ time_vs_kilocalories_scatter_by_strength.png
    â”œâ”€â”€ walks_kilocalories_vs_avg_hr.png
    â”œâ”€â”€ walks_kilocalories_vs_time.png
    â”œâ”€â”€ workouts_by_day_of_week.png
    â””â”€â”€ workouts_by_hour_of_day.png
```

---

## Methodology

### 1. Data Extraction

The raw Polar Flow export is a directory of JSON files. The extraction pipeline:

1. **File discovery:** List all files in `./data/` matching the pattern `training-session`.
2. **JSON parsing:** Load each file and extract nested workout data.
3. **Heart-rate statistics:** For each workout, compute:
   - `heartRateAvg2` â€” mean heart rate across all samples
   - `heartRateStd` â€” standard deviation of heart rate
   - `heartRateQ1`, `heartRateQ25`, `heartRateQ50`, `heartRateQ75`, `heartRateQ99` â€” quantiles at 1%, 25%, 50%, 75%, 99%

4. **Session metadata:** Extract start/stop time, sport, calories (`kiloCalories`), distance, and device-specific fields.

### 2. Data Cleaning & Transformation

| Step | Action | Rationale |
|------|--------|-----------|
| Drop columns | `zones`, `samples`, `autoLaps`, `laps`, `latitude`, `longitude`, `ascent`, `descent` | Privacy (GPS) + unused features |
| Datetime conversion | `startTime`, `stopTime` â†’ `datetime` | Enables duration calculation |
| Duration | `totalTime = (stopTime âˆ’ startTime)` in minutes | Standardized session length |
| Heart-rate extraction | `heartRateMax`, `heartRateAvg`, `heartRateMin` from nested dict | Flatten nested structure |
| Indoor flag | `isInside = True` if `distance` is null | Distinguish treadmill/stationary from outdoor |
| Activity type | `isStrength = True` if `'strength'` in sport name | Binary strength vs. cardio comparison |
| Sport categorization | Lowercase and `Categorical` | Consistent factor levels |
| Missing values | Drop rows with all-NaN heart-rate data | Incomplete sessions |

**Outlier removal (for modeling):**
- IQR method applied to `totalTime`, `kiloCalories`, `heartRateQ99` with `k=1.5`

### 3. Exploratory Analysis

| Analysis | Visualizations |
|----------|----------------|
| **Heart-rate distributions** | Histograms of `heartRateAvg`, `heartRateQ99`, `heartRateStd` |
| **Caloric trends over time** | Time series of `kiloCalories` with daily average line |
| **Intensity patterns** | Scatter: `heartRateQ1` vs. `heartRateQ99` colored by calories |
| **Workout timing** | Bar charts: workouts by hour of day, by day of week |
| **Activity comparison** | Scatter/jitter: `isStrength` vs. calories, heart rate, duration |
| **Sport-specific** | `walks_kilocalories_vs_time.png`, `walks_kilocalories_vs_avg_hr.png` |

### 4. Statistical Modeling

**Subset used for regression:**
- Features: `kiloCalories`, `totalTime`, `heartRateQ99`, `isStrength`, `sport`
- Running excluded (only 2 sessions)

**Model comparison:**

| Model | Formula | Objective |
|-------|---------|-----------|
| OLS (Time only) | `kiloCalories ~ totalTime` | Baseline: duration alone |
| OLS (By sport) | `kiloCalories ~ totalTime` per sport | Sport-specific calibration |
| OLS (Time + HR) | `kiloCalories ~ totalTime + heartRateQ99` | Add heart-rate predictor |
| Linear Mixed Model | `kiloCalories ~ totalTime + heartRateQ99` with random `~ totalTime | isStrength` | Account for activity-type variance |

**Diagnostic tests:**
- **VIF (Variance Inflation Factor):** All features < 10 â†’ no severe multicollinearity
- **Goldfeld-Quandt test:** Heteroscedasticity check
- **Shapiro-Wilk test:** Normality of residuals
- **Q-Q plot:** Visual normality assessment
- **Residual plots:** Standardized residuals vs. fitted values

---

## Key Findings

### Descriptive Statistics
- **283 workouts** analyzed over ~1 year
- **Total calories burned:** ~111,000 kcal (approximately equivalent to **14.4 kg of body fat** at 7,700 kcal/kg)
- **Sport breakdown:**
  | Sport | Calories Burned | Equivalent Fat |
  |-------|----------------|----------------|
  | Walking | 33,080 kcal | 4.3 kg |
  | Strength training | 31,547 kcal | 4.1 kg |
  | Treadmill running | 19,825 kcal | 2.6 kg |
  | Cycling | 4,029 kcal | 0.5 kg |
  | Running | 940 kcal | 0.1 kg |

### Timing Patterns
- Workout timing follows a **bimodal distribution** with peaks at **12:00** and **20:00**.
- No strong day-of-week preference observed.

### Activity Comparison
- **Heart-rate distributions** differ markedly between strength training and cardiovascular sessions.
- **Indoor sessions** (treadmill, stationary bike) show distinct heart-rate profiles compared to outdoor GPS-tracked activities.
- **Duration and calories** have a correlation of **0.92**, indicating session length is a dominant predictor.

### Model Results

| Model | RMSE | RÂ² | Notes |
|-------|------|-----|-------|
| OLS (Time only) | 79 | 0.85 | Duration alone explains 85% of variance |
| OLS (Treadmill running) | â€” | 0.96 | Excellent fit for indoor running |
| OLS (Cycling) | â€” | 0.98 | Excellent fit for cycling |
| OLS (Walking) | â€” | 0.82 | Moderate fit for walking |
| OLS (Strength training) | â€” | 0.44 | Poor fit â€” high variance in strength sessions |
| **Linear Mixed Model** | **61** | â€” | Best overall; accounts for activity-type variance |

> The mixed model reduced RMSE from 79 to 61 by modeling activity type as a random effect, capturing unobserved heterogeneity between strength and cardio sessions.

### Diagnostic Findings
- Residual diagnostics indicate the mixed model captures most variance.
- **Heteroscedasticity** present in some OLS specifications, justifying the mixed-model approach.
- **VIF** scores below 10 for all features â†’ no severe multicollinearity.

### Key Metrics Summary
| Metric | Value | Context |
|--------|-------|---------|
| Workouts analyzed | 283 | ~1 year of data |
| Activity types | 6 | Walking, strength, treadmill, cycling, running, other |
| Best model | Linear Mixed Model | RMSE 61 vs. 79 for OLS |
| OLS RÂ² (time only) | 0.85 | Duration alone |
| Cycling RÂ² | 0.98 | Best sport-specific fit |
| Strength training RÂ² | 0.44 | High variance |
| Durationâ€“calories correlation | 0.92 | Strong linear relationship |
| Total calories burned | ~111,000 kcal | ~14.4 kg body fat equivalent |

---

## Limitations

- **Small sample for some sports:** Running had only 2 sessions and was excluded; strength training RÂ² was low (0.44), suggesting high intra-session variance.
- **Missing data:** GPS and distance fields are absent for indoor sessions, limiting spatial analysis.
- **Selection bias:** Data reflects personal workout habits, not a randomized sample of exercise types.
- **Model generalizability:** The mixed model is fitted to one user's data; applying it to other athletes would require recalibration.
- **Outlier removal:** IQR-based filtering may have removed legitimate high-intensity sessions.

---

## Next Steps

- **Collect more data** across multiple users to build a generalized caloric expenditure model.
- **Add heart-rate zones** (from Polar's `zones` field) as features for intensity modeling.
- **Compare with metabolic equivalents (METs)** from published tables to validate predictions.
- **Time-series forecasting** of weekly/monthly calorie trends using Prophet or LSTM.
- **Deploy as a personal dashboard** with Streamlit for ongoing tracking.

---

## Technical Specifications

| Specification | Detail |
|--------------|--------|
| **Language** | Python 3 |
| **Data format** | JSON (Polar Flow export) |
| **Workouts analyzed** | 283 |
| **Duration span** | ~1 year |
| **Statistical models** | OLS, Linear Mixed Model (statsmodels) |
| **Diagnostics** | VIF, Goldfeld-Quandt, Shapiro-Wilk, Q-Q plots |
| **Outlier method** | IQR (k=1.5) |

---

## How to Run

### Prerequisites

```bash
pip install -r requirements.txt
```

### Steps

1. **Export data:** Log in to [Polar Flow](https://flow.polar.com) and export all training sessions as JSON files.
2. **Organize files:** Place JSON files in `./data/` (directory name may vary; update path in notebook).
3. **Run notebook:** Open `Polar.ipynb` in Jupyter and execute cells sequentially.

> **Note:** The raw dataset is not included in this repository for privacy reasons.

---

## Privacy Note

This project uses **personal health data** (heart-rate, caloric expenditure, GPS coordinates). The dataset is **not distributed** with this repository. To reproduce the analysis, you must export your own data from Polar Flow and point the notebook to your local `./data/` directory.

---

## Author

**Chandan saraswat**

- GitHub: [@Galeon12](https://github.com/Galeon12)
- LinkedIn: [in/Galeon12](https://linkedin.com/in/Galeon12)

---

<p align="center">
  <a href="../README.md">â† Back to repository root</a>
</p>
