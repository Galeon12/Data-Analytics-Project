# Data Analytics Projects

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.8%2B-blue?logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/R-4.0%2B-blue?logo=r&logoColor=white" alt="R">
  <img src="https://img.shields.io/badge/Jupyter-Notebook-orange?logo=jupyter&logoColor=white" alt="Jupyter">
  <img src="https://img.shields.io/badge/License-MIT-green" alt="License">
  <img src="https://img.shields.io/badge/Status-Active-success" alt="Status">
</p>

<p align="center">
  <strong>A portfolio of end-to-end data analytics and data science projects</strong> spanning pandemic epidemiology, real estate economics, global security, sports statistics, and quantitative finance.
</p>

---

## ðŸ“‘ Table of Contents

- [About This Repository](#about-this-repository)
- [Projects Overview](#projects-overview)
- [Technology Stack](#technology-stack)
- [Getting Started](#getting-started)
- [Repository Structure](#repository-structure)
- [Contributing](#contributing)
- [License](#license)
- [Author](#author)

---

## About This Repository

This repository contains a curated collection of **data analytics and data science projects** completed as part of an ongoing portfolio. Each project follows a rigorous workflow: problem formulation â†’ data acquisition â†’ cleaning â†’ exploratory analysis â†’ modeling â†’ interpretation.

The work spans multiple domains and tools, demonstrating proficiency in both **Python** and **R**, statistical modeling, machine learning, geospatial analysis, and financial time-series analytics.

---

## Projects Overview

| # | Project | Domain | Language | Focus |
|---|---------|--------|----------|-------|
| 1 | [ðŸ¦  COVID-19 Pandemic Analysis](#1-covid-19-pandemic-analysis) | Public Health | Python | Pandemic visualization & EDA |
| 5 | [ðŸ“ˆ FX Trading Algorithm Analysis](#5-fx-trading-algorithm-analysis) | Quantitative Finance | Python | Algorithm performance & risk metrics |

---

## Results Snapshot

| Project | Key Result |
|---------|-----------|
| COVID-19 | ~35M cases analyzed; rural pop vs. cases correlation: -0.46 |
| FX Trading | 92 trades; 40% win rate; Monte Carlo: 100K simulations |

---

## 1. COVID-19 Pandemic Analysis

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.8%2B-blue?logo=python" alt="Python">
  <img src="https://img.shields.io/badge/Status-Completed-green" alt="Status">
  <img src="https://img.shields.io/badge/Matplotlib-Visualization-blue" alt="Matplotlib">
</p>

A multi-notebook end-to-end exploratory data analysis of the COVID-19 pandemic. Combines JHU CSSE case time series with World Bank socioeconomic indicators to uncover trends across time, geography, and population health metrics.

### Key Highlights
- **Global scale:** ~35M confirmed cases analyzed as of October 2020
- **Multi-level analysis:** World, continent, and country-level breakdowns
- **Socioeconomic integration:** Correlates pandemic metrics with GDP, life expectancy, rural population, and healthcare expenditure
- **Reusable architecture:** Custom `CovidDataViz` class for reproducible plotting

[ðŸ“‚ View Project](covid19-pandemic-analysis/)

---

## 2. Trading Results Analysis

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.8%2B-blue?logo=python" alt="Python">
  <img src="https://img.shields.io/badge/Status-WIP-yellow" alt="Status">
  <img src="https://img.shields.io/badge/Quantitative-Finance-blue" alt="Finance">
</p>

Walk-forward performance analysis of an algorithmic trading system. Examines 92 trades across multiple instruments to evaluate profitability, risk characteristics, trade duration distributions, and statistical properties of returns.

### Key Highlights
- **92 trades** analyzed across multiple FX instruments
- **Monte Carlo simulation:** 100,000 samples to estimate forward performance
- **Distribution analysis:** Profit-per-lot characterized by skew, kurtosis, and fitted distributions
- **Timing edge:** Identified profitable intraday patterns (2pm, 4pm)

[ðŸ“‚ View Project](fx-trading-analysis/)

---

## Technology Stack

### Languages & Core Tools
| Tool | Purpose |
|------|---------|
| Python 3.8+ | Primary language for 4 of 5 projects |
| Jupyter Notebooks | All analysis and modeling |
| Git | Version control |

### Python Libraries
| Library | Domain | Projects |
|---------|--------|----------|
| Pandas | Data manipulation | All Python projects |
| NumPy | Numerical computing | All Python projects |
| Matplotlib | Visualization | All projects |
| Scipy | Statistics & distributions | Trading |
| wbdata | World Bank API access | COVID-19 |

---------|---------|
| tidyverse (`dplyr`, `ggplot2`, `tidyr`) | Data manipulation & viz |
| GGally | Matrix & pair plots |
| rworldmap / mapproj | Geospatial visualization |
| ggrepel | Non-overlapping text labels |
| lubridate | Date handling |
| scales | Axis formatting |

---

## Requirements by Project

Each project includes a `requirements.txt` (or `requirements.R` equivalent) for reproducible setup:

| Project | Install Command |
|---------|-----------------|
| COVID-19 | `pip install -r covid19-pandemic-analysis/requirements.txt` |
| FX Trading | `pip install -r fx-trading-analysis/requirements.txt` |

---

## Getting Started

### Prerequisites
- Python 3.8+
- Jupyter Notebook or JupyterLab

### Installation

```bash
# Clone the repository
git clone https://github.com/Galeon12/Data-Analytics-Projects-in-python.git
cd Data-Analytics-Projects-in-python

# Install Python dependencies
pip install pandas numpy matplotlib
```

Each project folder contains a dedicated `README.md` with specific setup instructions.

---

## Repository Structure

```
Data-Analytics-Projects-in-python/
â”œâ”€â”€ README.md                           # This file
â”œâ”€â”€ LICENSE
â”œâ”€â”€ covid19-pandemic-analysis/         # ðŸ¦  Pandemic visualization
â”‚   â”œâ”€â”€ data/
â”‚   â”‚   â””â”€â”€ download_data.py
â”‚   â”œâ”€â”€ features/
â”‚   â”‚   â”œâ”€â”€ make_all.py
â”‚   â”‚   â”œâ”€â”€ make_cases.py
â”‚   â”‚   â”œâ”€â”€ make_cases_daily_change.py
â”‚   â”‚   â”œâ”€â”€ make_cases_since_t0.py
â”‚   â”‚   â”œâ”€â”€ make_continents.py
â”‚   â”‚   â”œâ”€â”€ make_coordinates.py
â”‚   â”‚   â”œâ”€â”€ make_country_stats.py
â”‚   â”‚   â”œâ”€â”€ make_country_to_continent.py
â”‚   â”‚   â”œâ”€â”€ make_mortality.py
â”‚   â”‚   â”œâ”€â”€ make_world_bank.py
â”‚   â”‚   â””â”€â”€ utils.py
â”‚   â”œâ”€â”€ visualizations/
â”‚   â”‚   â””â”€â”€ covid_data_viz.py
â”‚   â”œâ”€â”€ notebooks/
â”‚   â”‚   â”œâ”€â”€ Data-wrangling.ipynb
â”‚   â”‚   â”œâ”€â”€ Exploratory-analysis-globally.ipynb
â”‚   â”‚   â”œâ”€â”€ Exploratory_analysis_fancy_plot.ipynb
â”‚   â”‚   â”œâ”€â”€ Exploratory-analysis-mortality.ipynb
â”‚   â”‚   â””â”€â”€ Exploratory_analysis_socioeconomic.ipynb
â”‚   â””â”€â”€ tests/
â”œâ”€â”€ cracow-real-estate-pricing/        # ðŸ  Real estate ML
â”‚   â”œâ”€â”€ 00_Data_Wrangling.ipynb
â”‚   â”œâ”€â”€ 00_Data_Wrangling.pdf
â”‚   â”œâ”€â”€ 01_Exploratory_Analysis.ipynb
â”‚   â”œâ”€â”€ 01_Exploratory_Analysis.pdf
â”‚   â”œâ”€â”€ 02_Model.ipynb
â”‚   â”œâ”€â”€ 02_Model.pdf
â”‚   â””â”€â”€ img/
â”œâ”€â”€ global-terrorism-eda/              # ðŸ’£ Terrorism EDA
â”‚   â”œâ”€â”€ Global Terrorism.ipynb
â”‚   â””â”€â”€ img/
â”œâ”€â”€ polar-watch-fitness-analysis/      # âŒš Sports statistics
â”‚   â”œâ”€â”€ Polar.ipynb
â”‚   â”œâ”€â”€ Polar.pdf
â”‚   â”œâ”€â”€ mdl_results.txt
â”‚   â””â”€â”€ img/
â””â”€â”€ fx-trading-analysis/               # ðŸ“ˆ Algorithmic trading
    â”œâ”€â”€ Trading Results Analysis.ipynb
    â”œâ”€â”€ Trading Results Analysis.pdf
    â””â”€â”€ img/
```

---

## Contributing

Contributions, issues, and feature requests are welcome. Please feel free to open an issue or submit a pull request.

1. Fork the project
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## License

Distributed under the MIT License. See [LICENSE](LICENSE) for more information.

---

## Author

**Chandan saraswat**

- GitHub: [@Galeon12](https://github.com/Galeon12)
- LinkedIn: [in/Galeon12](https://linkedin.com/in/Galeon12)

---

<p align="center">
  Built with â¤ï¸ and a lot of â˜•
</p>
