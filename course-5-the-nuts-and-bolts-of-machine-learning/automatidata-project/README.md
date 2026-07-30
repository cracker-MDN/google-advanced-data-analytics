# Automatidata — Predicting Generous Tippers (NYC TLC)

## Business Context

**Client:** New York City Taxi and Limousine Commission (TLC)
**Goal:** Predict whether a rider will be a *generous tipper* (tipping ≥20%), using only information available before or at the start of a ride, to help TLC and drivers understand what drives generous tipping.

This project continues the Automatidata × NYC TLC engagement from Courses 1–4. Having already explored the dataset (Courses 1–2), tested a payment-method hypothesis (Course 3), and built a fare-prediction regression model (Course 4), TLC asked Automatidata to shift focus toward gratuity.

An important reframing happened early: the original ask was a model predicting who **wouldn't** tip at all, intended to feed a driver-facing app. That objective was rejected on ethical grounds — it would function as a "riders to avoid" list, with a real risk of enabling discriminatory treatment. The project was reframed around identifying **generous** tippers instead, a meaningfully different — though not risk-free — objective. A random forest model built around this reframed objective was proposed to and approved by TLC stakeholders; the team also built and compared an XGBoost alternative during development, ultimately selecting it as the stronger of the two.

## PACE Framework

| Stage | Focus |
|---|---|
| **Plan** | Defined the business objective, external stakeholders, ethical considerations, and — after revision — F1 as the success metric, since false positives (broken driver expectations) and false negatives (a generous rider passed over) both carry real, comparable costs. |
| **Analyze** | Confirmed tree-based models require no linearity/normality assumptions (unlike Course 4's regression); screened every feature for pre-ride availability and target leakage; confirmed a near-balanced target class (52.6% / 47.4%). |
| **Construct** | Built and tuned Random Forest and XGBoost via 4-fold cross-validated grid search; selected XGBoost as champion based on consistent outperformance across all four metrics. |
| **Execute** | Validated the model's error pattern via confusion matrix, investigated an unexpected feature-importance finding, and cross-checked results against the official course exemplar before finalizing recommendations. |

Full reasoning for each stage is documented in the [PACE Strategy Document](./PACE-strategy-document.docx).

## Methodology

- **Data:** 2017 Yellow Taxi Trip data (22,699-row sample), merged with pre-ride fare/distance/duration predictions carried over from the Course 4 regression model.
- **Scope:** Restricted to credit-card trips (15,265 rows) — cash tips aren't recorded in this dataset, so there's no ground truth for cash-paying riders.
- **Target:** `generous` — binary, 1 if `tip_amount / (total_amount − tip_amount) ≥ 0.20`, else 0.
- **Features:** Pre-ride-only variables (vendor, passenger count, rate code, pickup/dropoff zone, pre-ride fare/distance/duration estimates, and engineered day/time-of-day/month features). All post-ride and target-derived columns (`trip_distance`, `fare_amount`, `tip_amount`, `total_amount`, etc.) were excluded to prevent data leakage.
- **Models:** Random Forest and XGBoost, both tuned via `GridSearchCV` (4-fold cross-validation, refit on F1).

## Key Results

| Model | Stage | Precision | Recall | F1 | Accuracy |
|---|---|---|---|---|---|
| Random Forest | Validation | 0.680 | 0.775 | 0.724 | 0.689 |
| Random Forest | Test | 0.690 | 0.790 | 0.737 | 0.703 |
| XGBoost | Validation | 0.692 | 0.784 | 0.735 | 0.702 |
| **XGBoost (champion)** | **Test** | **0.694** | **0.805** | **0.745** | **0.710** |

**Confusion matrix (XGBoost, test set):** 875 true negatives, 571 false positives, 313 false negatives, 1,294 true positives. False positives — the costlier error, per the Plan-stage cost analysis — outnumber false negatives nearly 2:1, even after optimizing for F1.

**Feature importance:** `VendorID` dominates every other feature by a wide margin. A direct check confirmed this is a real pattern, not a modeling artifact: riders are generous 30.7% of the time with one vendor's technology vs. 70.1% with the other's — a ~40-point gap most plausibly explained by differing in-cab tip-suggestion defaults on the two vendors' payment terminals, rather than any genuine difference in rider behavior.

**Validation against the course exemplar:** results were cross-checked against the official exemplar notebook and found consistent with — and slightly better than — its actual computed output (the exemplar's *executive summary* document contained a mismatched, stale results table; its notebook's real numbers align closely with ours). One notable difference: in the exemplar, Random Forest slightly outperformed XGBoost, the reverse of this project's result — a reminder that small hyperparameter and seed differences can flip which architecture wins, without either outcome being wrong.

## Caveats

- **Scope limited to credit-card riders** (~67% of trips) — the model has no validated performance on cash-paying riders.
- **False positives outnumber false negatives**, meaning the model more often overstates a rider's generosity than misses it — worth factoring into how confidently any single prediction is treated.
- **`VendorID`'s outsized importance may reflect payment-terminal design rather than rider behavior** — a finding worth sharing directly with TLC, since standardizing tip-prompt defaults across vendors could be a more direct lever than the predictive model itself.
- **Removing `VendorID` drops F1 to 0.612** — still above a coin-flip baseline on accuracy, but a reminder of how much of the model's apparent strength rests on that one feature.

## Recommendations

1. Deploy the model as an **informational signal only** — not a mechanism that determines who gets picked up or how a rider is treated — to guard against the location/vendor-based equity risks identified during Plan and Execute.
2. Share the `VendorID` finding with TLC directly and investigate whether standardizing in-cab tip-suggestion defaults across vendors would improve tipping outcomes more directly than any model.
3. Treat cash-rider coverage as a data-collection gap, not a current capability.

## Files

| File | Description |
|---|---|
| `Activity_Course5_Automatidata_project_lab.ipynb` | Full analysis notebook — feature engineering, model building, evaluation, and the VendorID validation check |
| `PACE-strategy-document.docx` | Complete PACE reasoning across all four stages |
| `exec_summary_final.pptx` | One-page stakeholder summary |
| `README.md` | This file |

## Next Steps

- Collect driver- and rider-level historical tipping data to test whether it out-predicts the current pre-ride feature set.
- Investigate the `VendorID` finding with TLC and the payment vendors directly.
- Explore classification threshold tuning to reduce the false-positive rate further.
