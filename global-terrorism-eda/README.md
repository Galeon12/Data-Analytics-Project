# Global Terrorism Database Analysis

<p align="center">
  <img src="https://img.shields.io/badge/R-4.0%2B-blue?logo=r&logoColor=white" alt="R">
  <img src="https://img.shields.io/badge/Tidyverse-EDA-blue?logo=tidyverse&logoColor=white" alt="Tidyverse">
  <img src="https://img.shields.io/badge/GGally-Matrix-blue" alt="GGally">
  <img src="https://img.shields.io/badge/rworldmap-Geospatial-blue" alt="rworldmap">
  <img src="https://img.shields.io/badge/Status-Completed-green" alt="Status">
</p>

<p align="center">
  <strong>An exploratory data analysis and geospatial visualization of the Global Terrorism Database (GTD)</strong><br>
  Covering the years 2007â€“2017, examining attack patterns, casualty distributions, weapon and target types, and the most active terrorist organizations.
</p>

---

## ðŸ“‘ Table of Contents

- [About This Project](#about-this-project)
- [Problem Statement](#problem-statement)
- [Data Source](#data-source)
- [Project Structure](#project-structure)
- [Methodology](#methodology)
  - [1. Data Loading & Subsetting](#1-data-loading--subsetting)
  - [2. Variable Selection & Cleaning](#2-variable-selection--cleaning)
  - [3. Missing Value Handling](#3-missing-value-handling)
  - [4. Feature Engineering](#4-feature-engineering)
  - [5. Exploratory Analysis](#5-exploratory-analysis)
- [Key Findings](#key-findings)
- [Visualizations](#visualizations)
- [Technical Specifications](#technical-specifications)
- [How to Run](#how-to-run)
- [Author](#author)

---

## About This Project

This project provides a comprehensive exploratory analysis of global terrorism incidents using the Global Terrorism Database (GTD). The analysis covers an 11-year window (2007â€“2017) and examines incidents from multiple dimensions: time, geography, attack modality, weapon type, target type, and perpetrator organization.

The project demonstrates:
- **Large-scale data cleaning** of a 170,000+ row dataset
- **Multidimensional aggregation** across categorical, numerical, and geospatial variables
- **Geospatial visualization** with annotation-style maps
- **Temporal trend analysis** with faceted time series

---

## Problem Statement

Terrorism is a complex, multidimensional phenomenon. The GTD contains over 170,000 incidents with rich attribute data. Key questions this project addresses:

1. Which countries, cities, and regions experienced the highest fatality counts?
2. What attack types and weapons are most lethal?
3. Which organizations were responsible for the greatest loss of life?
4. How did terrorism patterns evolve over the 2007â€“2017 period?
5. What are the characteristics of high-damage incidents?

---

## Data Source

| Source | Description | URL |
|--------|-------------|-----|
| Global Terrorism Database (GTD) | Incident-level data on global terrorism from 1970â€“2017 | [start.umd.edu/gtd](http://start.umd.edu/gtd/) |
| GTD Codebook | Variable definitions and coding rules | [Codebook PDF](http://start.umd.edu/gtd/downloads/Codebook.pdf) |
| GTD on Kaggle | Mirror of the full dataset used in this analysis | [Kaggle](https://www.kaggle.com/START-UMD/gtd) |

**Dataset file:** `./data/globalterrorismdb_0718dist.csv`

**Dataset scope:** The full GTD contains data from 1970â€“2017. This analysis filters to **2007â€“2017** and removes incidents with uncertain terrorist classification (`doubtterr == 1`).

---

## Project Structure

```
global-terrorism-eda/
â”œâ”€â”€ Global Terrorism.ipynb           # Single notebook: cleaning, EDA, viz
â”œâ”€â”€ img/
â”‚   â”œâ”€â”€ attacks_by_weapon.png
â”‚   â”œâ”€â”€ deaths_by_attack_over_time.png
â”‚   â”œâ”€â”€ deaths_by_target_over_time.png
â”‚   â”œâ”€â”€ deaths_by_weapon_over_time.png
â”‚   â”œâ”€â”€ group_attack_annotated_blue_sea.png
â”‚   â”œâ”€â”€ README.md
â”‚   â””â”€â”€ top_five_groups_percent_ts.png
```

---

## Methodology

### 1. Data Loading & Subsetting

1. Load the full GTD CSV using `read.csv()`.
2. **Filter to 2007â€“2017** (`iyear >= 2007 & iyear <= 2017`).
3. **Remove uncertain incidents:** Filter `doubtterr == 0` (incidents where terrorist classification was in doubt).

### 2. Variable Selection & Cleaning

**Columns selected for analysis:**

| Dimension | Variables |
|-----------|-----------|
| **Time** | `iyear`, `imonth`, `iday` |
| **Geospatial** | `latitude`, `longitude`, `city`, `country`, `region` |
| **Numerical** | `nperps`, `nkill`, `nwound`, `nkillter`, `propextent`, `ransomamt` |
| **Binary** | `doubtterr`, `vicinity`, `ishostkid`, `extended` |
| **Categorical** | `city`, `country`, `region`, `attacktype1_txt`, `weaptype1_txt`, `targtype1_txt` |
| **Text** | `gname` (terrorist group name) |

**Column renaming:**
- `iyear` â†’ `year`
- `imonth` â†’ `month`
- `iday` â†’ `day`
- `propextent` â†’ `damage`
- `latitude` â†’ `lat`
- `longitude` â†’ `long`
- `country_txt` â†’ `country`
- `region_txt` â†’ `region`
- `attacktype1_txt` â†’ `attack`
- `weaptype1_txt` â†’ `weapon`
- `targtype1_txt` â†’ `target`

**Label normalization (for readability):**

| Original Label | Normalized Label |
|----------------|------------------|
| `Facility/Infrastructure Attack` | `Infrastructure Attack` |
| `Bombing/Explosion` | `Explosion` |
| `Hostage Taking (Barricade Incident)` | `Hostage (Barricade)` |
| `Hostage Taking (Kidnapping)` | `Hostage (Kidnapping)` |
| `Vehicle (not to include vehicle-borne explosives...)` | `Vehicle` |
| `Government (General)` | `Government` |
| `Private Citizens & Property` | `Private` |
| `Democratic Republic of the Congo` | `Congo` |
| `Islamic State of Iraq and the Levant (ISIL)` | `ISIL` |
| `Al-Qaida in Iraq` | `Al-Qaida` |
| `Al-Nusrah Front` | `Al-Nusrah` |
| `Fulani extremists` | `Fulani` |
| `Houthi extremists (Ansar Allah)` | `Ansar Allah` |
| `Communist Party of India - Maoist (CPI-Maoist)` | `CPI - Maoist` |
| `Tehrik-i-Taliban Pakistan (TTP)` | `TTP` |

### 3. Missing Value Handling

GTD uses sentinel values for unknown/missing data:

- **Negative values** (`-9`, `-99`) replaced with `0` for:
  - `doubtterr`, `vicinity`, `extended`, `ishostkid`
  - `nperps`, `nkill`, `nwound`, `nkillter`, `ransomamt`, `damage`
- **Remaining NAs** imputed with `0` for count variables and `FALSE` for binary flags using `tidyr::replace_na()`.

### 4. Feature Engineering

| Feature | Description |
|---------|-------------|
| `date` | Constructed via `lubridate::make_date(year, month, day)`; month=0 â†’ 6, day=0 â†’ 15; rows with NA dates dropped |
| `damage` (encoded) | Categorical: `0/4 â†’ Unknown`, `3 â†’ Low`, `2 â†’ Medium`, `1 â†’ High` |

### 5. Exploratory Analysis

The analysis covers five analytical dimensions:

**A. Temporal Analysis**
- Date range validation: 2007-01-01 to 2017-12-31
- Daily and monthly fatality time series
- LOESS-smoothed trend lines

**B. Categorical Aggregations**
- Top 10 cities, countries, and regions by total fatalities (`nkill`)
- Top 5 attack types, weapon types, and target types by fatalities
- Top 10 terrorist groups by fatalities; primary target for each

**C. Numerical Summaries**
- Descriptive statistics for `nperps`, `nkill`, `nwound`, `nkillter`, `ransomamt`
- Log-transformed histograms (`log(1 + value)`) for skewed count variables
- Correlation matrix (`ggcorr`) for `nkill`, `nkillter`, `nperps`, `nwound`

**D. Time Series**
- Fatalities by attack type, weapon, and target over time (faceted plots)
- Cumulative monthly fatalities
- Group-specific activity timelines

**E. Geospatial**
- World map with annotated attack locations for the 5 most active groups
- Regional attack heatmaps

---

## Key Findings

### Geographic Concentration
- A small number of **cities and countries** accounted for the vast majority of fatalities.
- The **Middle East & North Africa** region had the highest total fatalities.
- **Baghdad**, **Mosul**, and **Kabul** were among the deadliest cities.

### Attack Modalities
- **Explosions/Bombings** and **armed assaults** were the most lethal attack types.
- **Firearms** and **explosives** dominated as weapon categories.
- **Private citizens** and **military/government** installations were the most frequently targeted.

### Perpetrator Organizations
| Rank | Organization | Primary Target |
|------|-------------|----------------|
| 1 | ISIL | Private citizens |
| 2 | Taliban | Military / Government |
| 3 | Al-Qaida | Military / Government |
| 4 | Boko Haram | Private citizens |
| 5 | Al-Nusrah | Government |

### Property Damage
- Most incidents recorded `Unknown` or `Low` damage levels.
- High-damage incidents were relatively rare but geographically concentrated.

### Numerical Patterns
- Strong right-skew in all count variables (`nkill`, `nwound`, `nperps`), requiring log transformation for visualization.
- Positive correlation between `nkill` and `nwound` (expected: incidents with many killed also tend to have many wounded).
- `nkillter` (terrorist deaths) highly correlated with `nkill` overall.

### Key Metrics Summary
| Metric | Value | Context |
|--------|-------|---------|
| Time window | 2007â€“2017 | 11 years of confirmed incidents |
| Full GTD size | ~170,000 rows | 1970â€“2017 |
| Filtered incidents | ~50,000+ | After removing uncertain classifications |
| Top region | Middle East & North Africa | Highest total fatalities |
| Top attack type | Explosions / Armed assaults | Most lethal modalities |
| Top weapon | Firearms / Explosives | Dominated casualty counts |

---

## Limitations

- **Selection bias:** Analysis excludes incidents marked `doubtterr == 1`, potentially undercounting ambiguous events.
- **Reporting gaps:** GTD coverage varies by region and year; some conflicts are underreported.
- **Label simplification:** Group names and attack types were manually normalized, introducing potential inconsistency.
- **Static snapshot:** This is a retrospective analysis; real-time monitoring would require automated data ingestion.
- **No causal inference:** Correlations between weapon type and fatalities do not imply weapon lethality â€” context matters.

---

## Next Steps

- **Automated GTD updates:** Script to pull latest GTD releases and regenerate visualizations.
- **Network analysis:** Model relationships between terrorist groups, targets, and locations using graph theory.
- **Predictive modeling:** Use historical patterns to forecast high-risk regions and attack types.
- **Interactive map:** Deploy as a Shiny app or Plotly dashboard with filters for year, region, and group.
- **Cross-database validation:** Compare GTD fatalities with UCDP or ACLED conflict data.

---

## Visualizations

| Visualization | Description |
|---------------|-------------|
| `attacks_by_weapon.png` | Bar chart of attacks by weapon type |
| `deaths_by_attack_over_time.png` | Time series of fatalities by attack type |
| `deaths_by_target_over_time.png` | Time series of fatalities by target type |
| `deaths_by_weapon_over_time.png` | Time series of fatalities by weapon type |
| `group_attack_annotated_blue_sea.png` | Geospatial map of top 5 group activities |
| `top_five_groups_percent_ts.png` | Stacked area chart of top 5 groups' share of fatalities over time |

---

## Technical Specifications

| Specification | Detail |
|--------------|--------|
| **Language** | R 4 |
| **Data manipulation** | tidyverse (`dplyr`, `tidyr`, `ggplot2`, `readr`) |
| **Geospatial** | rworldmap, mapproj |
| **Matrix visualization** | GGally (`ggcorr`) |
| **Labels** | ggrepel |
| **Dates** | lubridate |
| **Formatting** | scales, grid |
| **Dataset size** | ~170,000 rows (full GTD); filtered to ~50,000+ for 2007â€“2017 |
| **Time window** | 2007â€“2017 (11 years) |

---

## How to Run

### Prerequisites

```r
install.packages(c("tidyverse", "GGally", "rworldmap", "ggrepel", "mapproj", "lubridate", "scales", "grid"))
```

See `requirements.txt` for version-pinned package list.

### Execution

1. **Place dataset:** Download the GTD CSV from [Kaggle](https://www.kaggle.com/START-UMD/gtd) or [start.umd.edu](http://start.umd.edu/gtd/) and save it as `./data/globalterrorismdb_0718dist.csv`.

2. **Open notebook:** Launch Jupyter with an R kernel:
   ```bash
   jupyter notebook
   ```

3. **Run cells:** Execute `Global Terrorism.ipynb` sequentially. The notebook covers:
   - Data loading and subsetting
   - Variable cleaning and label normalization
   - Missing value imputation
   - Feature engineering (date construction, damage encoding)
   - All exploratory analyses and visualizations

---

## Author

**Chandan saraswat**

- GitHub: [@Galeon12](https://github.com/Galeon12)
- LinkedIn: [in/Galeon12](https://linkedin.com/in/Galeon12)

---

<p align="center">
  <a href="../README.md">â† Back to repository root</a>
</p>
