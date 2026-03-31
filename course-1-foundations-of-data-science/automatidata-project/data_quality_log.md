# Data Quality Log — NYC TLC 2017 Yellow Taxi Trip Data

**Project:** Automatidata x NYC TLC — Fare Prediction Model
**Stage:** Course 1 — Initial Dataset Inspection
**Dataset:** 2017 Yellow Taxi Trip Data (22,699 rows, 18 columns)

---

## Purpose

This log documents all data quality issues identified during the initial inspection of the TLC dataset. For each issue, it records how it was found, the reasoning behind the interpretation, and the recommended action before exploratory data analysis (EDA) begins.

This log serves as a reference for the data cleaning stage in Course 2.

---

## Issues Log

---

### Issue 1 — Negative Fare Amounts

| Field | Detail |
|-------|--------|
| **Column** | `fare_amount` |
| **How found** | `df.describe()` — minimum value of -$120.00 |
| **Scope** | Small number of records |

**Reasoning:**
A fare amount cannot logically be negative under normal circumstances. Negative values most likely represent payment reversals, refunds, or corrections applied to previously charged fares. This interpretation is supported by the presence of negative `total_amount` values in the same records, and the frequent appearance of `payment_type = 4` (Dispute) in the lowest-value rows when sorted by `total_amount`.

**Recommended action:**
Investigate the records with negative fare amounts. Cross-reference with `payment_type` to confirm whether these are disputed or reversed transactions. Consider removing or flagging these records before modeling, as they do not represent genuine completed trips.

---

### Issue 2 — Negative Total Amounts

| Field | Detail |
|-------|--------|
| **Column** | `total_amount` |
| **How found** | `df.describe()` — minimum value of -$120.30; `df.sort_values('total_amount', ascending=True).head(20)` |
| **Scope** | Small number of records |

**Reasoning:**
Similar to negative fare amounts, negative total amounts suggest payment reversals or dispute resolutions. Sorting by `total_amount` ascending confirmed that the majority of negative-value rows have `payment_type = 4` (Dispute), directly supporting the reversal interpretation.

**Recommended action:**
Same as Issue 1 — investigate and cross-reference with `payment_type`. These records should be excluded from fare prediction modeling.

---

### Issue 3 — Fare Amount System Cap ($999.99)

| Field | Detail |
|-------|--------|
| **Column** | `fare_amount` |
| **How found** | `df.describe()` — maximum value of $999.99 |
| **Scope** | Small number of records |

**Reasoning:**
The value $999.99 is suspiciously round for a metered fare. In data systems, exact round-number maximums typically indicate a system ceiling — the maximum value the recording system could store. The actual fare for these trips may have been higher but was capped at $999.99 at the point of recording. This is a data limitation rather than a data entry error.

**Recommended action:**
Flag these records as potentially unreliable for the upper range of fare prediction. Consider excluding them from model training or treating them as a separate category.

---

### Issue 4 — Zero Trip Distances

| Field | Detail |
|-------|--------|
| **Column** | `trip_distance` |
| **How found** | `df.describe()` — minimum value of 0.00; confirmed in `df.sort_values('trip_distance', ascending=False)` bottom rows |
| **Scope** | Multiple records |

**Reasoning:**
A trip distance of 0.00 miles is not physically meaningful for a completed taxi trip. Two likely explanations: (1) the taximeter was engaged but the trip was cancelled before the vehicle moved, or (2) the distance was not recorded properly by the system. Either way, these records do not represent genuine completed trips and would distort distance-based fare predictions.

**Recommended action:**
Investigate whether zero-distance trips have associated fare amounts. If they do, they likely represent data entry or system errors. Consider removing records where `trip_distance = 0` before modeling.

---

### Issue 5 — Unusually Long Trip Distance (33.96 miles)

| Field | Detail |
|-------|--------|
| **Column** | `trip_distance` |
| **How found** | `df.sort_values('trip_distance', ascending=False).head(10)` — maximum value of 33.96 miles |
| **Scope** | Very small number of records |

**Reasoning:**
NYC is approximately 35 miles at its widest point, so 33.96 miles is technically possible but highly unusual for a city taxi trip. The vast majority of trips fall under 10 miles, with a median of 1.61 miles. These long-distance trips are likely airport runs or out-of-city trips and may follow different fare structures (e.g., negotiated fares, RatecodeID = 5).

**Recommended action:**
Do not remove — these are plausible edge cases. However, flag them for separate analysis and consider whether they should be included in a general fare prediction model or treated as a separate category.

---

### Issue 6 — Zero Passenger Count

| Field | Detail |
|-------|--------|
| **Column** | `passenger_count` |
| **How found** | `df.describe()` — minimum value of 0 |
| **Scope** | Small number of records (6 records with count = 0 in credit card subset) |

**Reasoning:**
The data dictionary states that `passenger_count` is a **driver-entered value**. A count of 0 most likely reflects a driver who forgot to enter the passenger count, or entered it incorrectly. It is unlikely to represent a genuine trip with zero passengers, as the taxi fare system requires at least one passenger. This is a classic driver data entry error.

**Recommended action:**
Investigate whether zero passenger count records have otherwise normal trip data. If so, consider imputing with the median passenger count or flagging as unknown rather than removing the records entirely.

---

### Issue 7 — Invalid RatecodeID Value (99)

| Field | Detail |
|-------|--------|
| **Column** | `RatecodeID` |
| **How found** | `df.describe()` — maximum value of 99; valid range per data dictionary is 1–6 |
| **Scope** | Unknown — requires further investigation |

**Reasoning:**
The data dictionary specifies six valid rate codes (1 = Standard, 2 = JFK, 3 = Newark, 4 = Nassau/Westchester, 5 = Negotiated fare, 6 = Group ride). A value of 99 falls entirely outside this range and has no documented meaning. This is either a data entry error or an undocumented system code. Either way it cannot be reliably interpreted without further information from TLC.

**Recommended action:**
Query TLC to confirm whether 99 is a valid undocumented code or a data entry error. In the meantime, flag or exclude records with `RatecodeID = 99` from modeling.

---

### Issue 8 — Datetime Columns Stored as Object Type

| Field | Detail |
|-------|--------|
| **Columns** | `tpep_pickup_datetime`, `tpep_dropoff_datetime` |
| **How found** | `df.info()` — both columns show dtype `object` instead of `datetime64` |
| **Scope** | All 22,699 records |

**Reasoning:**
These columns contain datetime information but are stored as plain text strings (object type). This prevents any time-based calculations — including deriving trip duration (a key predictor for fare amount), extracting time of day, or identifying day of week patterns. Converting these columns to proper datetime type is essential before any time-based analysis or feature engineering.

**Recommended action:**
Convert both columns using `pd.to_datetime()` before EDA. Then derive a `trip_duration` column by subtracting pickup from dropoff datetime. This will be one of the two primary predictive variables for the fare model alongside `trip_distance`.

---

### Issue 9 — Cash Tips Not Recorded

| Field | Detail |
|-------|--------|
| **Column** | `tip_amount` |
| **How found** | Data dictionary review; confirmed by `df[df['payment_type']==2]['tip_amount'].mean()` returning 0.0 |
| **Scope** | All cash payment records (7,267 trips — 32% of dataset) |

**Reasoning:**
The data dictionary explicitly states that `tip_amount` is automatically populated for credit card tips only — cash tips are not included. This means the overall average tip of $1.84 across all records (from `df.describe()`) significantly underestimates actual tipping behavior, as it excludes tips from 32% of all trips. The average credit card tip alone is $2.73 — the true all-payment average would be higher still if cash tips were captured. This is a structural data limitation, not an error — it reflects how the payment system was designed.

**Recommended action:**
Flag `tip_amount` as an incomplete variable in any analysis involving tipping behavior. Do not use it as a reliable feature in fare prediction without acknowledging this limitation. If possible, recommend TLC explore ways to capture cash tip data in future data collection.

---

## Summary Table

| # | Column | Issue | Likely Cause | Priority |
|---|--------|-------|-------------|----------|
| 1 | `fare_amount` | Negative values (min -$120) | Payment reversals/disputes | High |
| 2 | `total_amount` | Negative values | Disputed payments | High |
| 3 | `fare_amount` | Max capped at $999.99 | System recording ceiling | Medium |
| 4 | `trip_distance` | Zero values | Cancelled trips/recording error | High |
| 5 | `trip_distance` | Max 33.96 miles | Long-distance edge case | Low |
| 6 | `passenger_count` | Zero values | Driver data entry error | Medium |
| 7 | `RatecodeID` | Value of 99 (invalid) | Data entry error/undocumented code | High |
| 8 | `tpep_pickup/dropoff_datetime` | Stored as object type | Not converted at ingestion | High |
| 9 | `tip_amount` | Cash tips not recorded | System design limitation | Medium |

---

*Log created during Course 1 initial inspection. To be updated as further issues are identified during EDA in Course 2.*
