# Google Advanced Data Analytics Certificate

Project work from the [Google Advanced Data Analytics Professional Certificate](https://www.coursera.org/professional-certificates/google-advanced-data-analytics), completed through Coursera.

This repository documents each course's end-of-course project. Courses 1–5 are built around a single fictional consulting scenario — **Automatidata**, a data consulting firm engaged by the **New York City Taxi and Limousine Commission (NYC TLC)** to build a fare prediction model — with each course adding a new analytical layer. Course 6, the program's capstone, shifts to an independent scenario: **Salifort Motors**, a fictional alternative-energy vehicle manufacturer, where the task is predicting and addressing employee turnover.

Each project folder contains a Jupyter notebook, a PACE strategy document, an executive summary presentation, and a README explaining business context, methodology, results, and next steps.

---

## Approach

Every project follows the **PACE framework**: Plan, Analyze, Construct, Execute. My workflow: reason through problems independently first, then check against the exemplar to identify gaps and refine. Written analytical answers aim to exceed exemplar depth where possible — specific numbers, cross-chart comparisons, appropriate caveats, and two-audience communication (technical for analytics teammates, non-technical for stakeholders).

---

## Course Progress

| # | Course | Focus | Status |
|---|---|---|---|
| 1 | Foundations of Data Science | Business scoping, initial data exploration, PACE framework | ✅ Complete |
| 2 | Go Beyond the Numbers: Translate Data into Insights | Exploratory data analysis, outlier handling, visualization (pandas, matplotlib, Tableau) | ✅ Complete |
| 3 | The Power of Statistics | Hypothesis testing, A/B testing, confidence intervals, Welch's t-test | ✅ Complete |
| 4 | Regression Analysis | Multiple linear regression, feature engineering, model evaluation | ✅ Complete |
| 5 | The Nuts and Bolts of Machine Learning | Classification, decision trees, random forests, XGBoost | ✅ Complete |
| 6 | Google Advanced Data Analytics Capstone | Capstone project — employee attrition prediction (Salifort Motors) | ✅ Complete |
| 7 | The Google Advanced Data Analytics Capstone | Final capstone deliverables | 🔒 Pending |

---

## Course Summaries

### Course 1 — Foundations of Data Science
**Automatidata project:** initial business scoping and data exploration of the 2017 Yellow Taxi Trip Data. Identified stakeholder needs, documented data quality issues (negative fares, zero trip distances, invalid RatecodeID values, datetime columns stored as objects), and identified `trip_distance` and `trip_duration` as the two primary predictive variables for the eventual fare prediction model.

**Deliverables:** notebook, PACE strategy document, executive summary, data quality log.

### Course 2 — Go Beyond the Numbers: Translate Data into Insights
**Automatidata project:** Python-based EDA using pandas and matplotlib. Cleaned the dataset, engineered initial features, produced visualisations showing fare distribution and outlier patterns, and documented findings for both technical and non-technical audiences.

**Deliverables:** notebook, PACE strategy document, executive summary.

### Course 3 — The Power of Statistics
**Automatidata project:** two-sample hypothesis test on whether mean fare amount differs between credit card and cash payments. Used Welch's two-sample t-test (t = 6.87, p = 6.8e-12) to reject the null hypothesis. Key analytical caveat: payment method is self-selected, so this is observational data — association only, not causation. Cash tips are not recorded in the dataset, a structural blind spot.

**Deliverables:** notebook, PACE strategy document, executive summary.

### Course 4 — Regression Analysis
**Automatidata project:** built a multiple linear regression model to predict NYC taxi fare amounts before the ride begins. Engineered route-level features (`mean_distance`, `mean_duration` per pickup-dropoff pair) available at prediction time. Model achieves **R² = 0.87** and **MAE = $2.10** on the test set. Verified all four regression assumptions. Documented caveats around multicollinearity between predictors (0.87), the JFK flat-rate structural artifact, and mild data leakage from computing route-level means on the full dataset.

**Deliverables:** notebook, PACE strategy document, executive summary.

### Course 5 — The Nuts and Bolts of Machine Learning
**Automatidata project:** built and compared Random Forest and XGBoost classifiers to predict whether a rider will be a generous tipper (≥20% tip). The originally proposed objective — predicting who *wouldn't* tip, for a driver-facing alert app — was rejected during planning on ethical grounds (it would function as a "riders to avoid" list) and reframed around identifying generous tippers instead. XGBoost was selected as champion (test F1 = 0.745, precision 0.694, recall 0.805), a result independently cross-checked against the official course exemplar's notebook and found consistent with — and slightly better than — its actual computed output. Key finding: `VendorID` emerged as the dominant predictive feature; a direct validation check confirmed this reflects a genuine ~40-point generosity gap between the two TLC payment-technology providers, most plausibly explained by differing in-cab tip-suggestion defaults rather than rider behavior itself — a caveat carried through to the final recommendations, alongside the model's credit-card-only scope and its tendency toward false positives over false negatives.

**Deliverables:** notebook, PACE strategy document, executive summary.

### Course 6 — Google Advanced Data Analytics Capstone
**Salifort Motors project:** a shift away from the Automatidata/NYC TLC scenario to an independent capstone scenario — Salifort Motors, a fictional alternative-energy vehicle manufacturer facing high employee turnover. Built and compared Decision Tree, Random Forest, and XGBoost classifiers to predict which employees are likely to leave. After removing 3,008 exact-duplicate rows (~20% of the dataset, concentrated among employees who left — dropping them shifted the measured attrition rate from 23.8% to 16.6%), EDA revealed attrition splits into at least three distinct employee profiles rather than one population: underworked and disengaged, overworked and burned out, and a third group that was overworked but still satisfied, likely leaving for reasons outside the dataset. `number_project` and `time_spend_company` both showed non-linear, U-shaped relationships with attrition — a direct violation of the linearity assumption a regression approach would require, and the reason tree-based models were used instead. XGBoost was selected as the final model (test recall 0.908, precision 0.936, F1 0.922, AUC 0.972) over a Decision Tree with marginally higher raw recall, since the Decision Tree's cross-validated recall was also nearly twice as variable across folds — a stability-adjusted choice rather than a raw-metric one. `satisfaction_level` was deliberately excluded as a predictor due to its potential entanglement with the outcome (self-reported at the same time as the decision to leave).

**Deliverables:** notebook, PACE strategy document, executive summary.

---

## Repository Structure

```
google-advanced-data-analytics/
│
├── README.md                                          (this file)
│
├── course-1-foundations-of-data-science/
│   └── automatidata-project/
│       ├── README.md
│       ├── Activity_Course1_Automatidata_project_lab.ipynb
│       ├── PACE-strategy-document.docx
│       ├── exec_summary_final.pptx
│       └── data_quality_log.md
│
├── course-2-go-beyond-the-numbers/
│   └── automatidata-project/
│       ├── README.md
│       ├── Activity_Course2_Automatidata_project_lab.ipynb
│       ├── PACE-strategy-document.docx
│       └── exec_summary_final.pptx
│
├── course-3-the-power-of-statistics/
│   └── automatidata-project/
│       ├── README.md
│       ├── Activity_Course3_Automatidata_project_lab.ipynb
│       ├── PACE-strategy-document.docx
│       └── exec_summary_final.pptx
│
├── course-4-regression-analysis/
│   └── automatidata-project/
│       ├── README.md
│       ├── Activity_Course4_Automatidata_project_lab.ipynb
│       ├── PACE-strategy-document.docx
│       └── exec_summary_final.pptx
│
├── course-5-the-nuts-and-bolts-of-machine-learning/
│   └── automatidata-project/
│       ├── README.md
│       ├── Activity_Course5_Automatidata_project_lab.ipynb
│       ├── PACE-strategy-document.docx
│       └── exec_summary_final.pptx
│
└── course-6-capstone/
    └── salifort-motors-project/
        ├── README.md
        ├── Activity_Course6_Salifort_Motors_project_lab.ipynb
        ├── PACE-strategy-document.docx
        └── exec_summary_final.pptx
```

---

## Tools and Libraries

- **Python** — pandas, numpy
- **Statistics** — scipy.stats, statsmodels
- **Modelling** — scikit-learn (LinearRegression, DecisionTreeClassifier, RandomForestClassifier, GridSearchCV, StandardScaler, train_test_split, metrics), XGBoost
- **Visualisation** — matplotlib, seaborn
- **Environment** — Jupyter Notebook

---

*Note: the Automatidata scenario and NYC TLC dataset, and the Salifort Motors capstone scenario, are provided by the Google Advanced Data Analytics Certificate for pedagogical purposes. Analysis and communication decisions are my own.*
