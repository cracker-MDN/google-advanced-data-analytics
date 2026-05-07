# Data Quality Log — NYC TLC 2017 Yellow Taxi Trip Data

**Project:** Automatidata x NYC TLC — Fare Prediction Model
**Dataset:** 2017 Yellow Taxi Trip Data (22,699 rows, 18 columns)
**Last updated:** Course 2 — Exploratory Data Analysis

---

## Purpose

This log documents all data quality issues identified during inspection of the TLC dataset. For each issue, it records how it was found, the reasoning behind the interpretation, recommended action, and current status as the project progresses through each course.

---

## Issues Log

---

### Issue 1 — Negative Fare Amounts

| Field | Detail |
|-------|--------|
| **Column** | `fare_amount` |
| **How found** | `df.describe()` — minimum value of -$120.00 |
| **Scope** | Small number of records |
| **Status** | ⚠️ Pending — to be removed in data cleaning stage |

**Reasoning:**
Negative values most likely represent payment reversals, refunds, or corrections. Confirmed by the frequent appearance of `payment_type = 4` (Dispute) in the lowest-value rows when sorted by `total_amount`.

**Recommended action:**
Exclude from modeling — these do not represent genuine completed trips.

---

### Issue 2 — Negative Total Amounts

| Field | Detail |
|-------|--------|
| **Column** | `total_amount` |
| **How found** | `df.describe()` — minimum value of -$120.30; `df.sort_values('total_amount', ascending=True).head(20)` |
| **Scope** | Small number of records |
| **Status** | ⚠️ Pending — to be removed in data cleaning stage |

**Reasoning:**
Negative total amounts suggest payment reversals or dispute resolutions. Sorting ascending confirmed the majority of negative rows have `payment_type = 4` (Dispute).

**Recommended action:**
Exclude from modeling — same treatment as Issue 1.

---

### Issue 3 — Fare Amount System Cap ($999.99)

| Field | Detail |
|-------|--------|
| **Column** | `fare_amount` |
| **How found** | `df.describe()` — maximum value of $999.99 |
| **Scope** | Small number of records |
| **Status** | ⚠️ Flagged — system limitation, not a data entry error |

**Reasoning:**
$999.99 is a suspiciously round maximum — typical of a system ceiling where the recording system cannot store values above this threshold. The actual fare may have been higher.

**Recommended action:**
Flag as potentially unreliable for upper-range fare prediction. Consider excluding from model training or treating as a separate category.

---

### Issue 4 — Zero Trip Distances

| Field | Detail |
|-------|--------|
| **Column** | `trip_distance` |
| **How found** | `df.describe()` — minimum value of 0.00; confirmed visually via box plot in Course 2 EDA |
| **Scope** | Multiple records |
| **Status** | ⚠️ Pending — to be removed in data cleaning stage |

**Reasoning:**
A trip distance of 0.00 miles is not physically meaningful. Either the trip was cancelled before the vehicle moved, or the distance was not recorded. Confirmed as the most important outlier to address before modeling.

**Recommended action:**
Remove records where `trip_distance = 0` before modeling.

---

### Issue 5 — Unusually Long Trip Distance (33.96 miles)

| Field | Detail |
|-------|--------|
| **Column** | `trip_distance` |
| **How found** | `df.sort_values('trip_distance', ascending=False).head(10)` |
| **Scope** | Very small number of records |
| **Status** | ✅ Retained — plausible edge case, flagged for separate analysis |

**Reasoning:**
NYC is approximately 35 miles at its widest point, so 33.96 miles is technically possible. Likely airport runs or out-of-city trips under negotiated fares (RatecodeID = 5).

**Recommended action:**
Retain but flag. Consider treating long-distance trips as a separate category in the fare model.

---

### Issue 6 — Zero Passenger Count

| Field | Detail |
|-------|--------|
| **Column** | `passenger_count` |
| **How found** | `df.describe()` — minimum value of 0 |
| **Scope** | Small number of records |
| **Status** | ⚠️ Pending — to be investigated in data cleaning stage |

**Reasoning:**
`passenger_count` is a driver-entered value. A count of 0 most likely reflects a driver who forgot to enter the count. These are real trips with real transactions — the 0 is a recording error, not an empty vehicle.

**Recommended action:**
Investigate whether these records have otherwise normal trip data. Consider imputing with median passenger count or flagging as unknown.

---

### Issue 7 — Invalid RatecodeID Value (99)

| Field | Detail |
|-------|--------|
| **Column** | `RatecodeID` |
| **How found** | `df.describe()` — maximum value of 99; valid range per data dictionary is 1–6 |
| **Scope** | Unknown — requires further investigation |
| **Status** | ⚠️ Pending — to be excluded from modeling until clarified |

**Reasoning:**
The data dictionary specifies six valid rate codes (1–6). A value of 99 falls entirely outside this range with no documented meaning.

**Recommended action:**
Query TLC to confirm whether 99 is a valid undocumented code or an error. In the meantime, exclude records with `RatecodeID = 99` from modeling.

---

### Issue 8 — Datetime Columns Stored as Object Type

| Field | Detail |
|-------|--------|
| **Columns** | `tpep_pickup_datetime`, `tpep_dropoff_datetime` |
| **How found** | `df.info()` — both columns showed dtype `object` instead of `datetime64` |
| **Scope** | All 22,699 records |
| **Status** | ✅ Resolved in Course 2 — converted using `pd.to_datetime()` |

**Reasoning:**
Stored as plain text strings, preventing time-based calculations. Conversion to datetime type was essential before extracting month, day, and trip duration features.

**Resolution (Course 2):**
Both columns converted using `pd.to_datetime()`. New derived columns created: `month`, `day_of_week` for time series analysis. `trip_duration` derivation noted for feature engineering in modeling stage.

---

### Issue 9 — Cash Tips Not Recorded

| Field | Detail |
|-------|--------|
| **Column** | `tip_amount` |
| **How found** | Data dictionary review; confirmed by `df[df['payment_type']==2]['tip_amount'].mean()` returning 0.0; visualized via histogram spike at $0 in Course 2 EDA |
| **Scope** | All cash payment records (7,267 trips — 32% of dataset) |
| **Status** | ⚠️ Structural limitation — cannot be fixed, flagged in all tip analysis |

**Reasoning:**
`tip_amount` is automatically populated for credit card tips only — cash tips are not captured. The overall mean tip of $1.84 significantly underestimates actual tipping. The $0 spike in the Course 2 tip histogram directly confirmed this limitation visually.

**Recommended action:**
Flag `tip_amount` as incomplete in any tipping analysis. Do not use as a reliable feature in fare prediction without acknowledging this limitation.

---

## Summary Table

| # | Column | Issue | Likely Cause | Priority | Status |
|---|--------|-------|-------------|----------|--------|
| 1 | `fare_amount` | Negative values (min -$120) | Payment reversals/disputes | High | ⚠️ Pending |
| 2 | `total_amount` | Negative values | Disputed payments | High | ⚠️ Pending |
| 3 | `fare_amount` | Max capped at $999.99 | System recording ceiling | Medium | ⚠️ Flagged |
| 4 | `trip_distance` | Zero values | Cancelled trips/recording error | High | ⚠️ Pending |
| 5 | `trip_distance` | Max 33.96 miles | Long-distance edge case | Low | ✅ Retained |
| 6 | `passenger_count` | Zero values | Driver data entry error | Medium | ⚠️ Pending |
| 7 | `RatecodeID` | Value of 99 (invalid) | Data entry error/undocumented code | High | ⚠️ Pending |
| 8 | `tpep_pickup/dropoff_datetime` | Stored as object type | Not converted at ingestion | High | ✅ Resolved |
| 9 | `tip_amount` | Cash tips not recorded | System design limitation | Medium | ⚠️ Flagged |

---

*Log updated through Course 2 EDA. Issue 8 resolved. All other issues pending data cleaning in Course 3.*
