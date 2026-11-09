# Marketing - Customer Lifetime Value (CLV) Prediction

**Type:** Regression

## Goal
An auto-insurance company wants to know how much each customer is worth. Predict the **Customer Lifetime Value (CLV)** so the marketing team can focus on high-value customers.

**Target:** `customer_lifetime_value` (USD)

## Data
The data is **synthetic** (fake, created with Python) so it can be big and have all the problems we want to practice on.

- **Generator:** `generate_data.py` (random seed = 123, so you always get the same data)
- **File:** `data/insurance_customer_value.csv` - about 120,000 rows (+0.5% duplicate rows) and 51 columns
- **Data types in the file:** int, float, text (object) and dates saved as text

Re-create (or change) the data:
```bash
python generate_data.py
```
Change `N_ROWS` (100,000 - 300,000) or `SEED` at the top of `generate_data.py` to get a bigger / different dataset.

### Problems added on purpose
- **ID / personal columns** (IDs, names, e-mail, phone, date of birth)
- **Messy column names** (`Annual Income ($)`, `CreditScore`, `No. of Dependents`, ...)
- **Short codes** that should be full values (`M`/`F`, `Y`/`N`, `T`/`F`, `FT`/`PT`, ...)
- **Numbers saved as text** (`$1,250.00`, `12,500`, `34 yrs`, `12.5 km`, `15%`)
- **Missing values**, also hidden as text (`N/A`, `unknown`, `?`, `-`, a space) and one **mostly empty** column
- **Outliers** (extreme values) and **impossible values** (age = 150, negative mileage, 9999 codes, ...)
- **Dates saved as text** in different formats
- **Messy text** (`New York`, `NEW YORK`, `  new york`)
- **Categories saved as numbers** (`region_code`)
- **Useless columns**: a constant column, an almost-constant column, a duplicate column
- **Duplicate rows** (~0.5%)

### Columns
| Column in the file | Clean name | Problems |
|---|---|---|
| `Customer` | `customer_id` | ID / personal detail -> drop |
| `Full Name` | `full_name` | ID / personal detail -> drop |
| `E-mail` | `email` | ID / personal detail -> drop |
| `Phone #` | `phone_number` | ID / personal detail -> drop |
| `DOB` | `date_of_birth` | ID / personal detail -> drop |
| `Age (Years)` | `age` | number saved as text (`12 yrs`); 1% missing; wrong values: `0`, `135` |
| `Gender` | `gender` | short codes: `M`, `F` |
| `Marital Status` | `marital_status` | short codes: `S`, `M`, `D`, `W` |
| `Education` | `education` | short codes: `HS`, `BSC`, `MSC`, `PHD` |
| `City` | `city` | mixed upper / lower case, extra spaces |
| `Region Code` | `region_code` | category saved as a number |
| `Preferred Language` | `preferred_language` | - |
| `Marketing Opt-In` | `marketing_opt_in` | short codes: `Y`, `N` |
| `Policy No.` | `policy_number` | ID / personal detail -> drop |
| `State` | `state` | 1% missing; mixed upper / lower case, extra spaces |
| `Coverage` | `coverage` | short codes: `BAS`, `EXT`, `PRE` |
| `EmploymentStatus` | `employment_status` | short codes: `EMP`, `UNE`, `MED`, `DIS`, `RET` |
| `Income` | `annual_income` | number saved as text (`$1,250.00`); 3% missing; extreme outliers |
| `Location Code` | `location_code` | short codes: `U`, `S`, `R` |
| `Vehicle Class` | `vehicle_class` | - |
| `Vehicle Size` | `vehicle_size` | - |
| `Monthly Premium Auto` | `monthly_premium_auto` | number saved as text (`$1,250.00`); wrong values: `0`, `9,999` |
| `Months Since Last Claim` | `months_since_last_claim` | 5% missing |
| `Months Since Policy Inception` | `months_since_policy_inception` | - |
| `Policy Start Date` | `policy_start_date` | date saved as text (`%m/%d/%Y`) |
| `Num Open Complaints` | `num_open_complaints` | - |
| `Number Of Policies` | `number_of_policies` | - |
| `Policy Type` | `policy_type` | - |
| `Policy Level` | `policy_level` | - |
| `Renew Offer Type` | `renew_offer_type` | - |
| `Sales Channel` | `sales_channel` | 2% missing; mixed upper / lower case, extra spaces |
| `Total Claim Amount` | `total_claim_amount` | number saved as text (`$1,250.00`); 2% missing; extreme outliers |
| `Vehicle Age Years` | `vehicle_age_years` | 3% missing |
| `Annual Mileage Km` | `annual_mileage_km` | number saved as text (`12 km`); 4% missing; wrong values: `-1`, `999,999` |
| `Num Drivers` | `num_drivers` | - |
| `Has Home Insurance` | `has_home_insurance` | short codes: `Y`, `N` |
| `Has Life Insurance` | `has_life_insurance` | short codes: `T`, `F` |
| `Paperless` | `paperless` | short codes: `Y`, `N` |
| `Payment Frequency` | `payment_frequency` | - |
| `Num Late Payments` | `num_late_payments` | - |
| `Customer Service Calls` | `customer_service_calls` | - |
| `NPS` | `nps_score` | 5% missing; wrong values: `-1`, `99` |
| `App Logins Month` | `app_logins_month` | - |
| `Responded To Last Campaign` | `responded_to_last_campaign` | - |
| `Loyalty Discount Percent` | `loyalty_discount_percent` | number saved as text (`12.5%`) |
| `Broker Code` | `broker_code` | 85% missing (mostly empty) |
| `Company` | `company` | only one value |
| `Currency` | `currency` | only one value |
| `Is Internal Test` | `is_internal_test` | almost only one value |
| `Drivers Count` | `drivers_count` | exact copy of `num_drivers` |
| `Customer Lifetime Value` | `customer_lifetime_value` | number saved as text (`$1,250.00`); **TARGET** |

## Files
| File | Description |
|---|---|
| `generate_data.py` | Creates the dataset |
| `customer_lifetime_value_prediction.ipynb` | The full project notebook |
| `data/insurance_customer_value.csv` | The dataset |
| `data/insurance_customer_value_metadata.xlsx` | Metadata (data dictionary): description, role, data types, missing values, statistics, codes and problems of every column |
| `models/` | Created when you run the notebook (best model, scaler, selected features) |

## Steps in the notebook
**A. Data cleaning**
- A0 Drop columns that are not useful (IDs, names, ...)
- A1 Rename columns
- A2 Data types: numbers saved as text, messy text, short codes, dates, categories saved as numbers
- A3 Missing values - % per column, drop columns > 50% missing, fill the rest (mean / mode)
- A4 Outliers - box plots, IQR table, wrong values -> median, extreme values -> capping
- A5 Duplicate rows and duplicate columns
- A6 Columns with only one value

**B. EDA**
- B1 Target histogram and box plot
- B2 Correlation heatmap and correlation with the target
- B3 Charts for the independent features (and features vs target)

**C. Feature engineering**
- C1 Binning / new features
- C2 Label encoding and one-hot encoding
- C3 Data splitting: 80% train / 15% test / 5% unseen (random_state = 123)
- C4 Feature scaling (StandardScaler)
- C5 Feature selection (SelectKBest + Random Forest importance)
- C6 20,000-row training sample for comparing / tuning the models

**D. Feature extraction**
- D1 PCA
- D2 Factor Analysis
- D3 Cluster analysis (K-Means with elbow and silhouette)

**E. Machine learning (20 models)**
- E1 Define the models
- E2 Evaluation function (all metrics)
- E3 Cross validation (5 folds)
- E4 Training and testing evaluation
- E5 Hyperparameter dictionary
- E6 Grid search tuning
- E7 Compare the models, select the best, re-train it on the full training data
- E8 Save the best model (`joblib`)
- E9 Load the model
- E10 Predict and evaluate on the 5% unseen data

## How to run
```bash
pip install pandas numpy matplotlib seaborn scikit-learn xgboost joblib jupyter
python generate_data.py
```
Then open `customer_lifetime_value_prediction.ipynb` **from this folder** and run all cells (the full notebook takes about 10-20 minutes).
