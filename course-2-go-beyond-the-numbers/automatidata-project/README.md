# Automatidata — NYC TLC Exploratory Data Analysis

## Project Overview

This project is part of the **Google Advanced Data Analytics Certificate** (Course 2: Go Beyond the Numbers — Translate Data into Insights). Building on the dataset inspection completed in Course 1, this stage focuses on exploratory data analysis (EDA), data cleaning, and visualization to understand taxi ridership patterns ahead of fare prediction modeling.

---

## Business Context

**Client:** New York City Taxi and Limousine Commission (TLC)
**Goal:** Build a regression model to predict taxi fare amounts before a ride.
**My role at this stage:** Perform EDA on the TLC dataset, clean data quality issues, build visualizations to identify patterns and outliers, and communicate findings to both technical and non-technical stakeholders.

---

## Framework

| Stage | What it covers in this project |
|-------|-------------------------------|
| **Plan** | Identifying outlier detection methods, selecting visualization types, assessing data for EDA |
| **Analyze** | Loading data, converting datetime columns, deriving new variables, computing summary statistics |
| **Construct** | Building box plots, histograms, bar charts, and time series visualizations in Python |
| **Execute** | Communicating EDA findings and outlier handling recommendations to the management team |

---

## Dataset

**Source:** NYC TLC 2017 Yellow Taxi Trip Data
**Size:** 22,699 rows × 18 columns

**New variables derived in this stage:**
- `trip_duration` — derived from `tpep_dropoff_datetime` minus `tpep_pickup_datetime`
- `month` — extracted from `tpep_pickup_datetime` for time series analysis
- `day_of_week` — extracted from `tpep_pickup_datetime` for daily pattern analysis

---

## Key EDA Findings

### Demand Patterns
- Ride demand peaks **Wednesday through Friday** and in **spring months (March–June)**
- Summer months (July–September) show lower demand — contrary to initial expectations, likely driven by NYC residents leaving the city rather than tourist influx
- Revenue patterns mirror ride counts — higher midweek and spring, lower summer and Sundays
- Thursday generates the highest total revenue of any weekday

### Trip Characteristics
- Trip distances are heavily **right-skewed** — median of 1.61 miles confirms most NYC taxi trips are short city rides, with a long tail of long-distance outliers
- 216 unique drop-off locations, unevenly distributed — a small number of high-demand zones (likely central Manhattan) account for the majority of rides

### Tipping Behavior
- Average tip amount is consistent across passenger counts ($1.53–$2.14) — group size does not meaningfully influence tipping
- The $0 spike in tip_amount histogram directly confirms cash tips are not recorded — tip analysis is structurally incomplete

### Outlier Observations
- Negative fare and total amounts visible in box plots — confirmed as payment reversals/disputes
- Zero trip distances confirmed as a key data quality issue requiring removal before modeling
- $999.99 fare cap visible as an isolated extreme value — system limitation, not an error

---

## Outlier Handling Strategy

| Outlier Type | Decision | Reasoning |
|---|---|---|
| Negative fare/total amounts | **Remove** | Payment reversals — not genuine trips |
| Zero trip distances | **Remove** | Cancelled or unrecorded trips |
| RatecodeID = 99 | **Remove** | Outside valid range of 1–6 |
| Fare cap at $999.99 | **Flag** | System limitation — actual fare may be higher |
| Trip distance > 30 miles | **Flag** | Plausible edge case — likely airport/outer borough |

---

## Visualizations Built

| Chart | Purpose |
|-------|---------|
| Box plots — `trip_distance`, `total_amount`, `tip_amount` | Outlier detection and distribution shape |
| Histograms — `trip_distance`, `total_amount`, `tip_amount` | Distribution analysis |
| Histogram — `tip_amount` by VendorID | Vendor comparison for tipping |
| Bar chart — mean tips by passenger count | Passenger count impact on tipping |
| Bar charts — rides and revenue by month | Seasonal demand patterns |
| Bar charts — rides and revenue by day of week | Weekly demand patterns |
| Bar chart — mean trip distance by drop-off location | Geographic distance patterns |
| Bar chart — ride count by drop-off location | Geographic demand concentration |
| Scatter plot (Tableau) — `trip_distance` vs `fare_amount` | Fare prediction relationship |

---

## Deliverables

| File | Description |
|------|-------------|
| `Activity_Course2_Automatidata_project_lab.ipynb` | Jupyter notebook — EDA, data cleaning, and visualizations |
| `Course2_PACE_strategy_document.docx` | PACE strategy document — reasoning and decisions at each project stage |
| `course2_exec_summary.pptx` | Executive summary — EDA findings and outlier handling recommendations |

---

## Tools & Libraries

- **Python** (pandas, numpy, matplotlib, seaborn)
- **Tableau Public** (scatter plot visualization)
- **Jupyter Notebook**
- **PACE Framework** (Google Advanced Data Analytics)

---

## Next Steps

1. **Data cleaning** — Remove records with negative fares, zero trip distances, and invalid RatecodeID values
2. **Feature engineering** — Derive `trip_duration` from datetime columns; extract time-of-day and day-of-week features
3. **Hypothesis testing** — Statistical analysis to confirm relationships between key variables before model building
4. **Supplementary data** — Incorporate weather data, live traffic data, and GPS-level location coordinates to strengthen the eventual fare prediction model

---

*This project is coursework completed as part of the Google Advanced Data Analytics Certificate. The dataset and scenario are fictional and provided for educational purposes.*
