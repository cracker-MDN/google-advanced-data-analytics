# Salifort Motors: Predicting Employee Turnover

**Google Advanced Data Analytics Certificate — Course 6 Capstone Project**

## Project Overview

Salifort Motors, a fictional alternative-energy vehicle manufacturer, is experiencing a high rate of employee turnover. Senior leadership commissioned an employee survey and asked the data team to (1) identify what's driving attrition and (2) build a model that predicts which employees are likely to leave, so HR can intervene proactively rather than reactively.

This project follows the full **PACE framework** (Plan → Analyze → Construct → Execute) to go from raw survey data to a deployed-ready predictive model and a set of concrete retention recommendations.

## Business Problem

High turnover is costly — Salifort invests heavily in recruiting, training, and upskilling employees, and losing them erodes that investment. Leadership needs to know **who is at risk of leaving and why**, so retention efforts can be targeted rather than blanket policy changes.

## The Data

- **Source:** [HR Analytics and Job Prediction dataset](https://www.kaggle.com/datasets/mfaisalqureshi/hr-analytics-and-job-prediction) (Kaggle)
- **Size:** 14,999 rows × 10 columns (self-reported employee survey data)
- **Target variable:** `left` (binary — did the employee leave the company)
- **Features:** satisfaction level, last performance evaluation, number of projects, average monthly hours, tenure, work accidents, promotions in the last 5 years, department, and salary tier

## Methodology

### Data Cleaning
- Renamed columns for clarity and consistency (fixed a typo in `average_montly_hours`, standardized casing)
- Identified and removed **3,008 exact-duplicate rows (~20% of the data)** — dropping them shifted the measured attrition rate from 23.8% to **16.6%**, revealing that duplicates were concentrated among employees who left rather than evenly distributed
- Applied IQR-based outlier detection on `time_spend_company`, flagging 824 rows (6+ years tenure) — later EDA showed this group is actually the *most loyal* segment, not a risk group, so these rows were retained for modeling

### Exploratory Data Analysis
Eight visualizations examined relationships between employee attributes and attrition. Key findings:

- **`number_project` and `average_monthly_hours` show a U-shaped, non-linear relationship with attrition** — both under- and over-allocation predict leaving, contradicting a simple "more work = more attrition" assumption
- **Three distinct leaver subpopulations** emerged from cross-referencing hours, projects, and satisfaction: underworked/disengaged, overworked/burned out, and a surprising overworked-but-satisfied group likely leaving for external opportunities
- **`satisfaction_level` is multimodal among leavers** (three separate peaks), meaning a single average satisfaction score misrepresents the group
- **Attrition risk peaks at exactly 5 years' tenure (45%)**, then drops to 0% for anyone with 7+ years
- **`salary` shows a clean, monotonic relationship** (20.5% attrition at low salary vs. 4.8% at high) — the one variable that behaved exactly as hypothesized
- **`department` is a weak predictor**, with attrition staying within a narrow 12–19% band across all departments

### Model Selection
Given the non-linear relationships found in EDA, **tree-based models were chosen over logistic regression**, since logistic regression's core assumption — a linear relationship between each predictor and the log-odds of the outcome — was directly contradicted by the U-shaped patterns in `number_project` and `time_spend_company`.

Three models were built and tuned via `GridSearchCV` (5-fold cross-validation, optimized for recall):

| Model | Accuracy | Precision | Recall | F1 | AUC |
|---|---|---|---|---|---|
| Decision Tree | 0.963 | 0.860 | **0.928** | 0.893 | 0.966 |
| Random Forest | 0.974 | 0.941 | 0.898 | 0.919 | 0.970 |
| **XGBoost (final)** | 0.974 | 0.936 | 0.908 | **0.922** | **0.972** |

`satisfaction_level` was deliberately excluded as a predictor — it's self-reported at the same time as the outcome, with a real risk that it reflects an already-made decision to leave rather than an independent driver of it.

### Why XGBoost Over Decision Tree
Decision Tree had the highest raw recall, but its cross-validated recall was also **nearly twice as variable** (±1.35% vs. XGBoost's ±1.07% across folds) — a single tree's higher variance makes that recall edge fragile rather than reliable. XGBoost gives up almost nothing on recall while offering the best overall discrimination (AUC) and more consistent performance expected on new data.

## Results

The final XGBoost model achieves:
- **90.8% recall** — correctly identifies the large majority of employees who actually leave
- **93.6% precision** — few false alarms
- **97.2% AUC** — strong overall discrimination between leavers and stayers

**Top predictors:** `number_project` and `time_spend_company` dominate feature importance, consistent with EDA findings; `department`, `promotion_last_5years`, and `work_accident` contribute comparatively little.

## Business Recommendations

1. **Reassess project-load policy** — both 2-project and 6–7 project assignments correlate strongly with leaving; 3–4 projects is the safest range.
2. **Build a targeted retention check-in around the 5-year tenure mark**, the single highest-risk point in the data.
3. **Treat the "satisfied but left" segment as a distinct problem** — since they show no dissatisfaction signal, standard engagement initiatives won't retain them; compensation and growth-path improvements are more likely levers.
4. **Deploy the model for proactive, supportive HR outreach only** — not as grounds for reduced opportunities or punitive action toward flagged employees.

## Repository Contents

| File | Description |
|---|---|
| `Activity_Course6_Salifort_Motors_project_lab.ipynb` | Full analysis: EDA, cleaning, model building, and evaluation |
| `PACE-strategy-document.docx` | Planning and reflection document across all four PACE stages |
| `exec_summary_final.pptx` | One-page stakeholder-facing summary of findings and recommendations |
| `README.md` | This file |

## Tools Used

Python (pandas, NumPy, Matplotlib, Seaborn, scikit-learn, XGBoost) in a Jupyter Notebook environment.

## Ethical Considerations

This model is intended to support proactive, positive HR interventions — not to penalize or surveil employees. Predictions should inform supportive outreach (e.g., a check-in conversation), never disciplinary action, reduced opportunities, or any adverse treatment of an individual based on a flagged prediction.

---

*Part of the [Google Advanced Data Analytics Professional Certificate](https://www.coursera.org/professional-certificates/google-advanced-data-analytics).*
