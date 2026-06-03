# Automatidata — NYC TLC A/B Test: Fare Amount by Payment Type

## Project Overview

This project is part of the **Google Advanced Data Analytics Certificate** (Course 3: The Power of Statistics). Building on the dataset inspection (Course 1) and exploratory data analysis (Course 2), this stage applies statistical methods to analyze the relationship between fare amount and payment type in the NYC TLC taxi trip dataset.

## Objective

Conduct an A/B test (two-sample t-test) to determine whether customers who pay by credit card pay statistically significantly higher fare amounts than customers who pay by cash. The analysis supports the broader Automatidata project goal of developing a fare prediction app for the New York City Taxi and Limousine Commission.

## Dataset

- **Source:** 2017 Yellow Taxi Trip Data (NYC TLC)
- **Size:** 408,294 rows × 18 columns
- **Key columns:** `fare_amount`, `payment_type` (1 = Credit card, 2 = Cash)

## Methodology

- **Test:** Two-sample t-test (Welch's) at α = 0.05 significance level
- **Groups:** Credit card customers vs. cash customers
- **Hypotheses:**
  - H₀: No difference in mean fare amount between credit card and cash payments
  - Hₐ: There is a difference in mean fare amount between credit card and cash payments

## Key Results

| Metric | Value |
|--------|-------|
| Credit card mean fare | $13.43 |
| Cash mean fare | $12.21 |
| t-statistic | 6.87 |
| p-value | 6.8e⁻¹² |
| Decision | Reject H₀ |

The difference in mean fare amount between credit card and cash payments is statistically significant.

## Recommendations

- **Encourage credit card payments** to increase fare revenue — ensure all vehicles have reliable card readers and consider small incentives for card use.
- **Investigate confounding variables** (trip distance, time of day) before attributing the fare difference to payment method alone.

## Caveats

- Customers self-select their payment method in practice — this is an observational study, not a true randomized experiment, so causal conclusions cannot be drawn.
- `Tip_amount` is only recorded for credit card payments; cash tips are excluded from the dataset.

## Deliverables

| File | Description |
|------|-------------|
| `Activity_Course3_Automatidata_project_lab.ipynb` | Jupyter notebook with descriptive statistics and hypothesis test |
| `PACE-strategy-document.docx` | PACE framework document (Plan, Analyze, Construct, Execute) |
| `exec_summary_final.pptx` | One-page executive summary for stakeholders |
| `README.md` | This file |

## Tools & Libraries

- Python (pandas, numpy, scipy, matplotlib)
- Jupyter Notebook
- pptxgenjs (executive summary)
