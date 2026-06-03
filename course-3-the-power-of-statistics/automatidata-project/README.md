# Automatidata — NYC TLC A/B Test: Fare Amount by Payment Type

## Project Overview

This project is part of the **Google Advanced Data Analytics Certificate** (Course 3: The Power of Statistics). Building on the dataset inspection (Course 1) and exploratory data analysis (Course 2), this stage applies statistical methods to analyze the relationship between fare amount and payment type in the NYC TLC taxi trip dataset.

---

## Business Context

**Client:** New York City Taxi and Limousine Commission (TLC)
**Goal:** Build a regression model to predict taxi fare amounts before a ride.
**My role at this stage:** Conduct an A/B test to determine whether customers who pay by credit card pay higher fare amounts than those who pay with cash, and communicate findings and recommendations to both technical and non-technical stakeholders.

---

## Framework

| Stage | What it covers in this project |
|-------|-------------------------------|
| **Plan** | Defining the research question, formulating null and alternative hypotheses, identifying the appropriate statistical test |
| **Analyze** | Computing descriptive statistics per payment type group, examining group means, sample sizes, and variability |
| **Construct** | Conducting a two-sample t-test (Welch's) to evaluate whether the fare difference is statistically significant |
| **Execute** | Interpreting results, communicating business insights and caveats, proposing actionable recommendations |

---

## Dataset

**Source:** NYC TLC 2017 Yellow Taxi Trip Data
**Size:** 408,294 rows × 18 columns
**Key columns:** `fare_amount` (meter-calculated fare), `payment_type` (1 = Credit card, 2 = Cash, 3 = No charge, 4 = Dispute, 5 = Unknown)

---

## Methodology

- **Test:** Two-sample t-test (Welch's) — does not assume equal variances between groups
- **Significance level:** α = 0.05
- **Groups:** Credit card customers vs. cash customers
- **Hypotheses:**
  - H₀: There is no difference in mean fare amount between credit card and cash payments
  - Hₐ: There is a difference in mean fare amount between credit card and cash payments

Welch's t-test was chosen over a standard two-sample t-test because the credit card and cash groups likely have different variances and sample sizes — Welch's adjusts the degrees of freedom accordingly, producing a more reliable p-value.

---

## Key Results

| Metric | Value |
|--------|-------|
| Credit card mean fare | $13.43 |
| Cash mean fare | $12.21 |
| Difference | $1.22 |
| t-statistic | 6.87 |
| p-value | 6.8e⁻¹² |
| Decision | Reject H₀ |

The difference in mean fare amount between credit card and cash payments is statistically significant — the p-value is far below the 0.05 threshold, providing strong evidence against the null hypothesis.

---

## Recommendations

- **Encourage credit card payments** to increase fare revenue — ensure all vehicles have reliable card readers and consider small incentives for card use
- **Investigate confounding variables** (trip distance, time of day, pickup/drop-off location) before attributing the fare difference to payment method alone
- **Do not draw causal conclusions** from this analysis — further controlled experimentation would be needed to establish causation

---

## Caveats

- **Observational study, not a true experiment:** Customers self-select their payment method — no random assignment to payment groups occurred. The observed difference may be driven by confounding variables (e.g., customers taking longer, more expensive trips may prefer credit cards)
- **Incomplete tip data:** `Tip_amount` is only recorded for credit card payments; cash tips are excluded from the dataset, meaning the economics of cash transactions are not fully captured
- **Assumption for this exercise:** The notebook assumes random assignment to payment groups for educational purposes, enabling A/B test methodology to be applied

---

## Deliverables

| File | Description |
|------|-------------|
| `Activity_Course3_Automatidata_project_lab.ipynb` | Jupyter notebook — descriptive statistics and hypothesis test |
| `PACE-strategy-document.docx` | PACE strategy document — reasoning and decisions at each project stage |
| `exec_summary_final.pptx` | Executive summary — A/B test results, recommendations, and caveats |
| `README.md` | This file |

---

## Tools & Libraries

- **Python** (pandas, numpy, scipy, matplotlib)
- **Jupyter Notebook**
- **PACE Framework** (Google Advanced Data Analytics)

---

## Next Steps

1. **Confounding variable analysis** — Investigate whether trip distance, time of day, or location explain the fare difference between payment types
2. **Regression modeling** — Build a multiple linear regression model to predict fare amounts using trip characteristics
3. **Feature engineering** — Leverage features identified in Courses 1–2 (trip_distance, trip_duration, time-based features) as predictors
4. **Model evaluation** — Assess model performance and iterate on feature selection

---

*This project is coursework completed as part of the Google Advanced Data Analytics Certificate. The dataset and scenario are fictional and provided for educational purposes.*
