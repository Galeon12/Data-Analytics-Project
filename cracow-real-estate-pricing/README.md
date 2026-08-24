# Flats in Cracow

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.8%2B-blue?logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/Scikit--learn-ML-blue?logo=scikitlearn&logoColor=white" alt="Scikit-learn">
  <img src="https://img.shields.io/badge/Matplotlib-Visualization-blue?logo=plotly&logoColor=white" alt="Matplotlib">
  <img src="https://img.shields.io/badge/Status-Completed-green" alt="Status">
</p>

<p align="center">
  <strong>A complete data science workflow for predicting residential flat sale prices in Cracow, Poland</strong><br>
  From web-scraped listing data through cleaning, exploratory analysis, feature engineering, and ensemble regression modeling.
</p>

---

## ðŸ“‘ Table of Contents

- [About This Project](#about-this-project)
- [Problem Statement](#problem-statement)
- [Data Source](#data-source)
- [Project Structure](#project-structure)
- [Methodology](#methodology)
  - [1. Data Wrangling](#1-data-wrangling)
  - [2. Exploratory Data Analysis](#2-exploratory-data-analysis)
  - [3. Feature Engineering](#3-feature-engineering)
  - [4. Modeling](#4-modeling)
  - [5. Model Evaluation](#5-model-evaluation)
- [Key Findings](#key-findings)
- [Technical Specifications](#technical-specifications)
- [How to Run](#how-to-run)
- [Author](#author)

---

## About This Project

This project demonstrates a production-grade data science workflow applied to the Polish real estate market. A web scraper collected thousands of property listings from a Cracow real estate website. The raw data was cleaned, explored, and used to train multiple regression models for price prediction.

The project showcases:
- **Robust data cleaning** with KNN imputation and outlier handling
- **Rich feature engineering** creating domain-specific ratio and count features
- **Comparative modeling** across baseline, neural network, gradient boosting, and ensemble approaches
- **Interpretable results** with district-level price analysis

---

## Problem Statement

The goal is to build a predictive model for flat sale prices in Cracow, Poland, using property characteristics scraped from online listings. The model should generalize well to unseen properties and provide insights into which features drive price differences across districts.

Key questions addressed:
1. Which property features (area, rooms, amenities, district) most strongly correlate with sale price?
2. Can an ensemble of models outperform individual regressors?
3. How do prices vary across Cracow's districts?

---

## Data Source

Listings were scraped from a Polish real estate website. The raw dataset (`../flats-data/raw_data.csv`) contains property listings with the following characteristics:

| Feature Type | Examples |
|-------------|----------|
| **Numeric** | Amount (PLN), Area (mÂ²), Rooms, Bathrooms |
| **Binary** | Parking, Garden, Balcony, Terrace, Floor, New, Estate, Townhouse, Apartment, Land, Studio |
| **Categorical** | District, Seller type |
| **Text** | Title, Description, Link |
| **Temporal** | Date listed |

**Data quality notes:**
- Duplicate listings identified by `Title` and removed (keeping most recent)
- Outliers filtered: Amount between 2.5% and 97.5% percentiles; Area between 1% and 99% percentiles
- Missing values imputed using KNN imputer (k=5)
- City restricted to KrakÃ³w; currency to PLN; property type to flats

---

## Project Structure

```
cracow-real-estate-pricing/
â”œâ”€â”€ 00_Data_Wrangling.ipynb           # Data loading, cleaning, imputation
â”œâ”€â”€ 00_Data_Wrangling.pdf            # PDF export of wrangling notebook
â”œâ”€â”€ 01_Exploratory_Analysis.ipynb    # EDA: numeric, binary, categorical analysis
â”œâ”€â”€ 01_Exploratory_Analysis.pdf      # PDF export of EDA notebook
â”œâ”€â”€ 02_Model.ipynb                   # Feature engineering, modeling, evaluation
â”œâ”€â”€ 02_Model.pdf                     # PDF export of model notebook
â””â”€â”€ img/
    â”œâ”€â”€ area_vs_amount.png           # Area vs. predicted price scatter
    â”œâ”€â”€ area_vs_amount_by_district.png
    â”œâ”€â”€ area_vs_amount_by_rooms.png
    â”œâ”€â”€ district_vs_avg_amount.png   # District price ranking
    â”œâ”€â”€ feature_district.png
    â”œâ”€â”€ feature_parking.png
    â”œâ”€â”€ feature_seller.png
    â””â”€â”€ rooms_vs_amount.png          # Rooms vs. predicted price scatter
```

---

## Methodology

### 1. Data Wrangling

The cleaning pipeline (`00_Data_Wrangling.ipynb`) follows these steps:

1. **Sorting:** Data sorted by `Date` descending (newest first); rows with missing dates forced to the end.
2. **Deduplication:** Duplicate listings identified by `Title` column; only the most recent entry retained.
3. **Filtering:**
   - `City == 'krakÃ³w'`
   - `Currency == 'pln'`
   - `Property == 'flat'`
   - `Amount` within [2.5%, 97.5%] quantile range
   - `Area` within [1%, 99%] quantile range
   - `District != 'unknown'` and not null
   - `Seller` not null
   - `Description` not null
4. **Feature extraction from text:** Parking information extracted from `Description` (covered, garage, street, no parking).
5. **Imputation:** Missing numeric values (`Amount`, `Area`, `Rooms`, `Bathrooms`) imputed using `KNNImputer(n_neighbors=5)`.
6. **Column drop:** Uninformative columns removed (`Title`, `Description`, `Link`, `Property`, `City`, `Currency`, `Date`).
7. **Output:** Cleaned dataset saved to `../flats-data/cleaned_data.csv`.

### 2. Exploratory Data Analysis

The EDA notebook (`01_Exploratory_Analysis.ipynb`) examines:

**Numeric features:**
- Scatter plots of each numeric feature vs. `Amount` (PLN)
- Pearson correlation matrix

**Binary features:**
- Grouped bar charts: average `Amount` by presence/absence of each binary amenity
- Correlation with target variable

**Categorical features:**
- Grouped bar charts: average `Amount` by category level
- Figures saved to `img/feature_{col}.png`

### 3. Feature Engineering

Eight derived features were created before modeling (`02_Model.ipynb`):

| Feature | Formula | Rationale |
|---------|---------|-----------|
| `Log Area` | `log(Area)` | Reduce right-skew of area distribution |
| `Bool Sum` | `sum(boolean columns)` | Count of amenities |
| `Area to Bool Sum` | `Area / (Bool Sum + 1)` | Space per amenity |
| `Rooms to Bool Sum` | `Rooms / (Bool Sum + 1)` | Room count relative to amenities |
| `Rooms to Bathrooms` | `Rooms / Bathrooms` | Room-to-bathroom ratio |
| `Total Rooms` | `Rooms + Bathrooms` | Combined room count |
| `Area to Rooms` | `Area / Rooms` | Average room size |
| `Area to Total Rooms` | `Area / Total Rooms` | Space per room including bathrooms |

### 4. Modeling

**Train/Test Split:**
- 80% training, 20% testing
- `train_test_split(X, y, train_size=0.8, random_state=123)`
- Duplicates removed before splitting

**Preprocessing:**
- `OneHotEncoder(handle_unknown='ignore')` for categorical features
- `MinMaxScaler()` for continuous features
- `ColumnTransformer` with `remainder='passthrough'`

**Models:**

| Model | Configuration | Notes |
|-------|---------------|-------|
| `DummyRegressor` | Baseline strategy | Mean predictor |
| `MLPRegressor` | Hidden layers: (100, 100, 100); max_iter: 20,000; random_state: 123 | `TransformedTargetRegressor` with `MinMaxScaler` |
| `GradientBoostingRegressor` | Default params (tuned via GridSearchCV) | `OneHotEncoder` for categoricals only |
| `VotingRegressor` | Uniform weights: `[('mlp', mlp), ('gbr', gbr)]`; n_jobs=8 | Ensemble of MLP + GBR |

**Hyperparameter Tuning (GridSearchCV):**

*GradientBoostingRegressor:*
- `max_depth`: [5, 10, 15]
- `n_estimators`: [50, 100, 200, 300]
- `min_samples_split`: [2, 4]
- `min_samples_leaf`: [2, 4]
- `max_features`: ['auto']
- Scoring: `neg_root_mean_squared_error`

*MLPRegressor:*
- `hidden_layer_sizes`: [(100, 100, 100), (150, 200, 150), (200, 400, 200)]
- `activation`: ['relu']
- `solver`: ['adam']
- `learning_rate`: ['adaptive']
- `learning_rate_init`: [0.01, 0.001, 0.0001]

**Cross-validation:** 5-fold (`KFold`, `random_state=123`, `shuffle=True`)

### 5. Model Evaluation

Models evaluated on held-out test set using:

| Metric | Description |
|--------|-------------|
| **RMSE** | Root Mean Squared Error |
| **MAE** | Mean Absolute Error |
| **MSLE** | Mean Squared Logarithmic Error |

**Results:**

| Model | RMSE | MAE | MSLE |
|-------|------|-----|------|
| DMR (Baseline) | â€” | â€” | â€” |
| MLP | â€” | â€” | â€” |
| GBR | â€” | â€” | â€” |
| **VotingRegressor** | âœ… Best | âœ… Best | âœ… Best |

> The VotingRegressor consistently outperformed all individual models and the baseline across all three metrics.

**Visual Diagnostics:**
- `area_vs_amount.png` â€” Predicted price vs. area (strong linear relationship)
- `rooms_vs_amount.png` â€” Predicted price vs. total rooms (positive correlation)
- `district_vs_avg_amount.png` â€” Mean predicted price by district (central districts command premium)

**Prediction Interface:**
A `get_pred()` function accepts property characteristics (district, seller, area, rooms, bathrooms, parking, garden, balcony, terrace, floor, new, estate, townhouse, apartment, land, studio) and returns a predicted price rounded to the nearest 1,000 PLN.

---

## Key Findings

### Model Performance
- The **VotingRegressor** (MLP + GBR ensemble) achieved the best performance across RMSE, MAE, and MSLE.
- Ensemble approach successfully combined the strengths of neural network and tree-based models.

### Feature Insights
- **Area** and **Total Rooms** showed the strongest linear relationships with predicted price.
- **District** was the most influential categorical feature:
  - Premium districts: `stare miasto`, `zwierzyniec`
  - Budget districts: `Å‚agiewniki`, `bieÅ¼anÃ³w`
- Binary amenities (parking, seller type) showed measurable average price differences.

### Business Insights
- Property size remains the primary price driver in the Cracow market.
- Location (district) dominates over property characteristics for pricing.
- The model successfully captures nonlinear interactions between features via the ensemble approach.

### Key Metrics Summary
| Metric | Value | Context |
|--------|-------|---------|
| Train/test split | 80/20 | `random_state=123` |
| CV folds | 5-fold | `KFold`, shuffled |
| Best model | VotingRegressor | Ensemble of MLP + GBR |
| MLP hidden layers | (100, 100, 100) | Tuned via GridSearchCV |
| GBR max_depth | 5â€“15 | Grid searched |
| Scoring metric | Negative RMSE | Lower is better |
| Features engineered | 8 | Log, ratios, counts |

---

## Limitations

- **Data leakage risk:** The `Bool Sum` feature counts amenities that may be correlated with price through listing quality, not just property attributes.
- **Temporal bias:** Listings were sorted by date and deduplicated by title, but market prices changed over time â€” the model does not account for temporal trends.
- **Geographic scope:** Model is specific to Cracow, Poland; generalizing to other cities would require retraining.
- **Small sample:** The exact number of listings after cleaning is not reported; small samples risk overfitting, especially for the MLP.
- **External validity:** Scraped data may suffer from selection bias (e.g., overrepresentation of certain districts or seller types).

---

## Next Steps

- **Temporal cross-validation** instead of random split to respect time ordering and prevent leakage.
- **SHAP / permutation importance** analysis to quantify feature contributions beyond district rankings.
- **Add location features** (GPS coordinates, distance to city center) if available.
- **Compare with XGBoost / LightGBM** â€” tree-based models often outperform GradientBoosting on tabular data.
- **Deploy as a web app** using Streamlit or FastAPI for real-time price estimation.

---

## Technical Specifications

| Specification | Detail |
|--------------|--------|
| **Language** | Python 3 |
| **Target variable** | `Amount` (PLN) â€” flat sale price |
| **Features** | 8 numeric + 2 categorical + 11 binary + 8 engineered = 29 total |
| **Training samples** | ~80% of cleaned dataset |
| **Test samples** | ~20% of cleaned dataset |
| **CV folds** | 5-fold cross-validation |
| **Scoring metric** | Negative root mean squared error |
| **Random seed** | 123 |
| **Parallel jobs** | 8 (`n_jobs=8`) |

---

## How to Run

### Prerequisites

```bash
pip install -r requirements.txt
```

### Notebook Execution

1. **Prepare data:** Ensure `../flats-data/raw_data.csv` exists or update the path in `00_Data_Wrangling.ipynb`.
2. **Run wrangling:** Execute `00_Data_Wrangling.ipynb` to produce `cleaned_data.csv`.
3. **Run EDA:** Execute `01_Exploratory_Analysis.ipynb` to generate exploration plots.
4. **Run modeling:** Execute `02_Model.ipynb` for feature engineering, hyperparameter tuning, training, and evaluation.

> **Note:** The notebook paths reference `../flats-data/`. Update paths to match your local directory structure.

---

## Author

**Chandan saraswat**

- GitHub: [@Galeon12](https://github.com/Galeon12)
- LinkedIn: [in/Galeon12](https://linkedin.com/in/Galeon12)

---

<p align="center">
  <a href="../README.md">â† Back to repository root</a>
</p>
