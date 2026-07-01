# NYC TLC Fare Prediction — Multiple Linear Regression Model

**Course 4: Regression Analysis — Simplify Complex Data Relationships**
Google Advanced Data Analytics Certificate · Automatidata Scenario

---

## Business Context

Automatidata is a data consulting firm engaged by the **New York City Taxi and Limousine Commission (NYC TLC)** to build a fare prediction model. The end goal is to give riders **upfront fare transparency** — showing an expected fare at the point of booking, before the ride begins — and to support operational planning at TLC.

The 2017 Yellow Taxi Trip Data used for this project contains **~408,000 trip records** across 18 columns, including fare amount, trip distance, duration, pickup and dropoff locations, payment type, and rate codes.

**External stakeholders:**
- Juliana Soto — Finance and Administration Department Head, NYC TLC
- Titus Nelson — Operations Manager, NYC TLC

**Internal Automatidata team:** Udo Bankole (Director of Data Analysis), Deshawn Washington (Data Analysis Manager), Uli King (Senior Project Manager), Luana Rodriguez (Senior Data Analyst).

---

## PACE Framework

| Stage | Focus |
|---|---|
| **Plan** | Identify the outcome variable (fare amount, continuous), confirm the modelling approach (multiple linear regression), review prior EDA and data quality findings |
| **Analyze** | Data cleaning, outlier handling, feature engineering (route-level aggregates, trip duration, rush-hour indicator), pairwise correlation analysis, multicollinearity assessment |
| **Construct** | Train-test split (80/20), standardisation via StandardScaler (fit on training only), fit OLS multiple linear regression, evaluate on both train and test |
| **Execute** | Interpret coefficients (unscaled for stakeholder communication), verify assumptions via residual diagnostics, produce executive summary and recommendations |

---

## Methodology

### Data cleaning
- **Negative fare_amount and duration values** (payment reversals / recording errors) imputed to `0`. The dataset already contains legitimate `$0` fares (cancelled trips), so `0` is a defensible substitute.
- **Extreme outliers** capped using IQR-based winsorization with a factor of 6 (`Q3 + 6 × IQR`) rather than the default 1.5. The standard rule would have removed many legitimate long trips; the higher factor retains realistic fares while removing `$999`-type recording errors.
- **Datetime columns** converted from object to datetime type using `pd.to_datetime()`.

### Feature engineering
- **`duration`** derived from pickup and dropoff datetimes.
- **`mean_distance`** and **`mean_duration`** computed as route-level averages using a `pickup_dropoff` grouping — these are the primary predictors, since they are available at prediction time (unlike raw distance and duration, which are only known after the ride).
- **`rush_hour`** binary indicator for weekday rides during 06:00–10:00 or 16:00–20:00.
- **`day`** and **`month`** extracted from pickup datetime (used during EDA, then dropped before modelling).

### Modelling
- **Multiple Linear Regression (OLS)** using scikit-learn's `LinearRegression`.
- **Predictors:** `mean_distance`, `mean_duration`, `rush_hour`, `VendorID` (dummy-encoded).
- **Train-test split:** 80% training, 20% test, `random_state=0` for reproducibility.
- **Standardisation:** `StandardScaler` fit only on training data, then transform applied to both train and test.

---

## Key Results

| Metric | Value | Interpretation |
|---|---|---|
| **R²** | 0.87 (test set) | Model explains ~87% of variance in fare amount |
| **MAE** | $2.1 | Predictions are typically within ~$2 of actual fares |
| **MSE** | 14.36 | Squared error term (used to derive RMSE) |
| **RMSE** | $3.8 | Errors weighted toward larger deviations |

- **Distance is the strongest predictor.** After unscaling the standardised coefficient back to original units, fare increases by approximately **$2 for every additional mile travelled**, holding other variables constant.
- **Train and test scores are similar** — indicating little bias and no overfitting.
- **Residuals are approximately normally distributed** and centred near zero (mean ≈ −0.015), confirming unbiased predictions.
- **Regression assumptions verified:** linearity, independence, normality of residuals, and homoscedasticity all met on the trained model.

---

## Caveats and Limitations

- **Multicollinearity:** `mean_distance` and `mean_duration` are highly correlated (Pearson = 0.87). This weakens individual coefficient interpretability but does not affect predictive accuracy. Ridge regularisation would stabilise the coefficients while retaining both predictors.
- **JFK flat-rate fares** (`RatecodeID = 2`) violate the linearity assumption — the fare is fixed at $52 regardless of distance or duration. The current model fits these linearly; the recommended fix is to impute $52 directly for these trips after prediction.
- **Data leakage:** `mean_distance` and `mean_duration` were computed on the full dataset before splitting, allowing some test-set information to influence training. Best practice is to compute means only on training data. Estimated impact: ~0.03 inflation in R².
- **Cash tips are not recorded** in the source dataset — a structural blind spot that limits any tip-related analysis.
- **Under-represented itineraries:** some pickup-dropoff route combinations have limited data, reducing prediction accuracy for those specific routes.

---

## Recommendations

1. **Deploy as an upfront fare estimator** — show riders an expected fare at booking using pickup and dropoff zones, day of week, time of day, and rush-hour status. All of these are known before the ride begins.
2. **Handle JFK trips separately.** For `RatecodeID = 2`, impute the $52 flat rate directly rather than relying on the linear model.
3. **Communicate the MAE transparently** — inform riders that estimates typically land within ~$2 of the actual fare to build trust.
4. **Retrain periodically** on newer data to reflect changes in pricing structures, traffic patterns, and demand.
5. **Request additional data** for under-represented pickup-dropoff routes to improve coverage.

---

## Next Steps

- Test **Ridge regression** to stabilise coefficients given the multicollinearity between `mean_distance` and `mean_duration`.
- Recompute `mean_distance` and `mean_duration` using **only the training set** to eliminate data leakage.
- Explore additional features such as **weather conditions**, **event schedules**, or **holiday indicators**.
- Consider handing off predictions to a downstream classification model (e.g., predicting tip generosity or ride cancellation likelihood).

---

## Files in This Project

| File | Description |
|---|---|
| `Activity_Course4_Automatidata_project_lab.ipynb` | Full Jupyter notebook with cleaning, EDA, feature engineering, modelling, and evaluation |
| `Course4_PACE_strategy_document.docx` | PACE strategy document covering all four project stages |
| `course4_executive_summary.pptx` | One-page executive summary for TLC stakeholders |
| `README.md` | This file |

---

## Tools and Libraries

- **Python** — pandas, numpy
- **Modelling** — scikit-learn (`LinearRegression`, `StandardScaler`, `train_test_split`, `metrics`)
- **Visualisation** — matplotlib, seaborn

---

*Part of the Google Advanced Data Analytics Certificate — Automatidata scenario, Course 4.*
