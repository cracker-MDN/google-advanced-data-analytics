# Automatidata — NYC TLC Dataset Inspection

## Project Overview

This project is part of the **Google Advanced Data Analytics Certificate** (Course 1: Foundations of Data Science). It is set in a fictional workplace scenario where I take on the role of a data analyst at **Automatidata**, a data consulting firm engaged by the **New York City Taxi and Limousine Commission (TLC)** to develop a fare prediction model.

The TLC dataset contains trip records from over 200,000 taxi and limousine licensees, making approximately one million combined trips per day. This project focuses on the earliest stage of the data pipeline — inspecting, understanding, and preparing the dataset before any modelling work begins.

---

## Business Context

**Client:** New York City Taxi and Limousine Commission (TLC)
**Goal:** Build a regression model to predict taxi fare amounts before a ride, based on trip distance, time, and other relevant variables.
**My role at this stage:** Inspect the TLC dataset, identify key variables, flag data quality issues, and prepare initial findings for the team ahead of exploratory data analysis (EDA).

---

## Framework

This project follows the **PACE framework** — a structured approach to data analytics projects:

| Stage | What it covers in this project |
|-------|-------------------------------|
| **Plan** | Reviewing the data dictionary, understanding the dataset structure, identifying relevant variables |
| **Analyze** | Loading the dataset, inspecting data types, computing summary statistics, investigating key variables |
| **Construct** | Not applicable at this stage |
| **Execute** | Communicating findings to stakeholders via executive summary and team briefing |

---

## Dataset

**Source:** NYC TLC 2017 Yellow Taxi Trip Data (provided for pedagogical purposes)

| Property | Detail |
|----------|--------|
| Rows | 22,699 trip records |
| Columns | 18 variables |
| Period | 2017 |

**Key variables for fare prediction:**
- `trip_distance` — elapsed trip distance in miles (taximeter)
- `tpep_pickup_datetime` / `tpep_dropoff_datetime` — used to derive trip duration
- `PULocationID` / `DOLocationID` — pickup and dropoff taxi zones
- `fare_amount` — target variable for the prediction model

---

## Key Findings

### Statistical Insights
- Median fare amount: **$9.50** (mean of $13.03 is skewed upward by outliers)
- Median trip distance: **1.61 miles** (mean of 2.91 miles skewed by long-distance outliers)
- Credit card is the dominant payment method at **67% of trips**
- Average credit card tip: **$2.73** — cash tips are not recorded, making this an underestimate of actual tipping behavior

### Data Quality Issues
Several issues were identified that require investigation and cleaning before modeling:

| Issue | Column(s) | Finding | Action Required |
|-------|-----------|---------|-----------------|
| Negative fare amounts | `fare_amount` | Min value of -$120 | Investigate — likely refunds or payment reversals |
| Negative total amounts | `total_amount` | Negative values present | Investigate — linked to disputed payments (payment_type = 4) |
| System cap on fare | `fare_amount` | Max value of $999.99 | Flag — likely a system ceiling, actual fare may be higher |
| Zero trip distances | `trip_distance` | Min value of 0.00 | Investigate — likely unrecorded or cancelled trips |
| Invalid rate code | `RatecodeID` | Max value of 99 (valid range: 1–6) | Remove or recode — outside valid range |
| Zero passenger count | `passenger_count` | Min value of 0 | Investigate — likely driver data entry error |
| Datetime stored as object | `tpep_pickup_datetime`, `tpep_dropoff_datetime` | Object type, not datetime | Convert to datetime for time-based analysis |
| Cash tips not recorded | `tip_amount` | Only credit card tips captured | Flag as data limitation — mean tip is an underestimate |

### Outlier Pattern
A scatter plot of `trip_distance` vs `total_amount` reveals a notable pattern — several trips with very short distances show unusually high total amounts. These are likely negotiated fares (RatecodeID = 5) or data errors requiring further investigation.

---

## Deliverables

| File | Description |
|------|-------------|
| `Activity_Course1_Automatidata_project_lab.ipynb` | Jupyter notebook — data loading, inspection, summary statistics, visualizations |
| `Course1_PACE_strategy_document.docx` | PACE strategy document — reasoning and decisions at each project stage |
| `exec_summary_final.pptx` | Executive summary — one-page professional summary with visual evidence for stakeholders |
| `data_quality_log.md` | Structured log of all data quality issues identified, with reasoning and recommended actions |

---

## Tools & Libraries

- **Python** (pandas, numpy, matplotlib)
- **Jupyter Notebook**
- **PACE Framework** (Google Advanced Data Analytics)

---

## Next Steps

Before exploratory data analysis can begin:

1. **Clean data quality issues** — investigate and resolve negative fares, zero distances, zero passenger counts, invalid RatecodeID values, and the $999.99 fare cap
2. **Convert datetime columns** from object to datetime type to enable time-based feature engineering (trip duration, time of day, day of week)
3. **Gather supplementary data** to strengthen the eventual model:
   - Live traffic data (trip duration under congestion)
   - Weather data (demand and trip duration impacts)
   - GPS-level location coordinates (more precise than zone IDs)
   - Multi-year and seasonal trip data for broader pattern recognition
   - Recorded cash tip data for complete tip analysis

---

*This project is coursework completed as part of the Google Advanced Data Analytics Certificate. The dataset and scenario are fictional and provided for educational purposes.*
