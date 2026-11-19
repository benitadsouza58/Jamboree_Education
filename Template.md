# LoanTap Personal Loan Default Prediction
## Data Understanding & Preparation Log

<text-placeholder></text-placeholder>

**Document status:** In progress – EDA completed for major variables; preprocessing pending.

---

# 1. Project Information

| Item | Details |
|---|---|
| Project name | LoanTap Personal Loan Default Prediction |
| Business problem | Analyse personal loan characteristics and identify factors associated with loan default. Develop a classification model to predict whether a loan will be fully paid or charged off. |
| Dataset name | LoanTapData.csv |
| Data source | LoanTap case-study dataset supplied for the project |
| Analyst | Project owner |
| Analysis stage | Exploratory Data Analysis |
| Problem type | Binary Classification |
| Target variable | `loan_status` |

**Business objective:**

Understand the characteristics and financial profiles associated with loan defaults and develop a model that can support loan-risk assessment.

The model will classify loan outcomes as:

- Fully Paid = 0
- Charged Off = 1

---

# 2. Dataset Overview

## 2.1 Dataset structure

| Item | Details |
|---|---:|
| Number of records | 396,030 |
| Number of original columns | 27 |
| Target variable | loan_status |
| Target classes | 2 |
| Fully Paid records | 318,357 |
| Charged Off records | 77,673 |

## 2.2 Target variable distribution

| Target category | Count | Percentage |
|---|---:|---:|
| Fully Paid | 318,357 | 80.39% |
| Charged Off | 77,673 | 19.61% |
| **Total** | **396,030** | **100%** |

**Initial observation:**

Approximately one in five loans was charged off. The target classes are imbalanced, with Fully Paid representing the majority class.

**Potential implication:**

During modelling, we should not rely on accuracy alone. We should also evaluate metrics such as precision, recall, F1-score, ROC-AUC and the confusion matrix.

## 2.3 Feature classification

**Numerical features**

- loan_amnt
- int_rate
- installment
- annual_inc
- dti
- open_acc
- pub_rec
- revol_bal
- revol_util
- total_acc
- mort_acc
- pub_rec_bankruptcies

**Categorical features**

- term
- grade
- sub_grade
- emp_title
- emp_length
- home_ownership
- verification_status
- purpose
- title
- initial_list_status
- application_type

**Date features**

- issue_d
- earliest_cr_line

**Potential identifier / high-cardinality feature**

- address

**Target**

- loan_status

---

# 3. EDA Findings Register

## 3.1 Univariate analysis – Numerical variables

Histograms, boxplots and descriptive statistics were used to understand distributions, spread, skewness and unusual observations.

| Feature | Observation / Finding | Potential Implication |
|---|---|---|
| loan_amnt | Median is $12,000; range is $500–$40,000. | Retain; assess scaling if required by the model. |
| int_rate | Median is 13.11%; range is 5.32–30.99. | Retain; examine relationship with target. |
| installment | Median is approximately $375.43. | Highly related to loan amount; investigate redundancy. |
| annual_inc | Highly right-skewed; skewness approximately 41.04. | Consider log transformation or robust scaling. |
| dti | Highly skewed; skewness approximately 431.05; maximum is 9,999. | Investigate extreme values and possible anomalies. |
| revol_bal | Right-skewed; skewness approximately 11.73; maximum is $1,743,266. | Retain legitimate high balances; consider transformation. |
| revol_util | Most observations are within a more typical range, but maximum is 892.3. | Investigate extreme observations. |
| pub_rec | Zero public derogatory records is the most common value. | Retain; assess relationship with target. |

## 3.2 Univariate analysis – Categorical variables

| Feature | Important finding |
|---|---|
| term | 36-month loans account for approximately 76.26% of records. |
| grade | Grade B is the most common category. |
| sub_grade | B3 is the most common sub-grade. |
| emp_length | 10+ years is the most common employment-length category. |
| home_ownership | MORTGAGE is the most common category, representing approximately 50.08%. |
| verification_status | Verified is the most common category. |
| purpose | Debt consolidation accounts for approximately 59.21% of loans. |
| initial_list_status | `f` is more common than `w`. |
| application_type | Individual applications account for approximately 99.82% of records. |

## 3.3 Bivariate analysis – Numerical variables vs target

| Feature | Charged Off | Fully Paid | Main observation |
|---|---:|---:|---|
| Interest rate – Mean | 15.88 | 13.09 | Charged-off loans have a higher mean interest rate. |
| Annual income – Median | $59,000 | $65,000 | Fully Paid borrowers have a higher median income. |
| DTI – Median | 19.34 | 16.34 | Charged-off borrowers have higher median DTI. |
| Revolving utilisation – Median | 59.3 | 53.7 | Charged-off borrowers have higher median utilisation. |
| Credit history – Mean | 184.34 months | 190.20 months | Fully Paid borrowers have modestly longer credit histories. |

**Interpretation:**

These variables show differences between the two target groups. The findings indicate associations, not proof of causation.

## 3.4 Bivariate analysis – Categorical variables vs target

### Grade

| Grade | Charged Off % | Fully Paid % |
|---|---:|---:|
| A | 6.29 | 93.71 |
| B | 12.57 | 87.43 |
| C | 21.18 | 78.82 |
| D | 28.87 | 71.13 |
| E | 37.36 | 62.64 |
| F | 42.79 | 57.21 |
| G | 47.84 | 52.16 |

**Finding:** Charged-off rates generally increase as the grade moves from A to G.

### Sub-grade

- A1 has the lowest charged-off rate at 2.87%.
- E5 has a charged-off rate of 40.31%.
- G3 has the highest observed charged-off rate at 51.09%.
- The overall pattern increases from A1 towards G5, with a few minor reversals.

**Finding:** Sub-grade has a strong association with loan repayment outcomes.

### Verification status

| Category | Charged Off % | Fully Paid % |
|---|---:|---:|
| Not Verified | 14.64 | 85.36 |
| Source Verified | 21.47 | 78.53 |
| Verified | 22.32 | 77.68 |

**Finding:** Not Verified loans have the lowest observed charged-off rate. The difference does not establish that verification itself influences default.

### Loan purpose

| Purpose | Charged Off % |
|---|---:|
| Small business | 29.45 |
| Renewable energy | 23.40 |
| Moving | 23.48 |
| Medical | 21.71 |
| Other | 21.22 |
| Debt consolidation | 20.74 |
| House | 19.72 |
| Vacation | 18.92 |
| Home improvement | 17.01 |
| Credit card | 16.71 |
| Major purchase | 16.47 |
| Educational | 16.34 |
| Car | 13.48 |
| Wedding | 12.09 |

**Finding:** Small business loans have the highest observed charged-off percentage. Some purpose categories, such as renewable energy and education, have relatively small sample sizes and require caution.

### Employment length

- Charged-off rates range approximately from 18.42% to 20.69% across employment-length categories.
- Less than one year has the highest observed rate at 20.69%.
- 10+ years has the lowest rate at 18.42%.
- There is no strong monotonic pattern.

**Finding:** Employment length shows relatively small differences in default rates across categories.

### Home ownership

| Category | Charged Off % |
|---|---:|
| RENT | 22.66 |
| NONE | 22.58 |
| OWN | 20.68 |
| MORTGAGE | 16.96 |
| OTHER | 14.29 |
| ANY | 0.00 |

**Finding:** Default rates vary across home-ownership categories. Some categories may have very small sample sizes; their counts should be checked before drawing conclusions.

### Initial list status

| Category | Charged Off % | Fully Paid % |
|---|---:|---:|
| Fractional (`f`) | 19.31 | 80.69 |
| Whole (`w`) | 20.08 | 79.92 |

**Finding:** The difference is small, approximately 0.77 percentage points.

### Application type

| Category | Charged Off % | Fully Paid % | Count |
|---|---:|---:|---:|
| INDIVIDUAL | 19.61 | 80.39 | 395,319 |
| JOINT | 12.71 | 87.29 | 425 |
| DIRECT_PAY | 35.66 | 64.34 | 286 |

**Finding:** Individual applications dominate the dataset. Joint and Direct Pay have small sample sizes, so their observed default rates should be interpreted cautiously.

## 3.5 Correlation analysis

Important numerical correlations identified:

| Feature pair | Correlation |
|---|---:|
| loan_amnt – installment | 0.953929 |
| pub_rec – pub_rec_bankruptcies | 0.699408 |
| open_acc – total_acc | 0.680728 |
| total_acc – mort_acc | 0.381072 |
| loan_amnt – annual_inc | 0.336887 |
| installment – annual_inc | 0.330381 |
| loan_amnt – revol_bal | 0.328320 |

**Findings:**

- `loan_amnt` and `installment` have a very strong positive correlation.
- `pub_rec` and `pub_rec_bankruptcies` are strongly associated.
- `open_acc` and `total_acc` are also strongly associated.

**Potential implication:** Investigate feature redundancy and multicollinearity during model preparation. Do not remove variables based on correlation alone.

## 3.6 Feature cardinality

| Feature | Unique values | Preliminary assessment |
|---|---:|---|
| term | 2 | Low cardinality |
| grade | 7 | Low cardinality |
| sub_grade | 35 | Moderate cardinality; ordinal structure |
| emp_title | 173,105 | Very high cardinality |
| emp_length | 11 | Low cardinality |
| home_ownership | 6 | Low cardinality |
| verification_status | 3 | Low cardinality |
| issue_d | 115 | Date feature |
| loan_status | 2 | Target |
| purpose | 14 | Low cardinality |
| title | 48,816 | Very high cardinality |
| earliest_cr_line | 684 | Date feature |
| initial_list_status | 2 | Low cardinality |
| application_type | 3 | Low cardinality |
| address | 393,700 | Very high cardinality; potential identifier |

**Finding:** `emp_title`, `title` and `address` should not be directly one-hot encoded in the initial model without further investigation.

---

# 4. Data Quality Assessment

## 4.1 Missing values

| Feature | Missing Count | Missing % | Investigation / Observation | Proposed Treatment |
|---|---:|---:|---|---|
| mort_acc | 37,795 | 9.54% | Median = 1; mode = 0; right-skewed. | Median imputation, fitted on training data. |
| emp_title | 22,927 | 5.79% | 173,105 unique values. | Exclude from initial model; reassess if useful aggregation is possible. |
| emp_length | 18,301 | 4.62% | Missing records have a higher charged-off rate. | Retain missing values as Unknown category. |
| title | 1,756 | 0.44% | 48,816 unique values; overlaps with purpose. | Exclude from initial model; reassess if needed. |
| pub_rec_bankruptcies | 535 | 0.14% | Median and mode are 0. | Median imputation, fitted on training data. |
| revol_util | 276 | 0.07% | Median = 54.8; mean = 53.79. | Median imputation, fitted on training data. |

**Important finding – Missing employment length:**

| Group | Charged Off % | Fully Paid % |
|---|---:|---:|
| Employment length available | 19.23 | 80.77 |
| Employment length missing | 27.53 | 72.47 |

Missing employment-length information is associated with a higher charged-off rate. Missingness may itself carry predictive information.

## 4.2 Duplicate records

**Status: Not yet investigated.**

Pending checks:
- Exact duplicate rows.
- Duplicate loan identifiers, if an appropriate identifier is available.
- Whether duplicate records represent genuine repeated observations or data duplication.

## 4.3 Outliers and anomalies

### Annual income

| Metric | Value |
|---|---:|
| Q1 | $45,000 |
| Q3 | $90,000 |
| IQR | $45,000 |
| Upper IQR bound | $157,500 |
| Potential outliers | 16,700 |
| Outlier percentage | 4.22% |

Additional investigation:
- 65 borrowers report annual income above $1 million.
- Of these, 57 were Fully Paid and 8 were Charged Off.

**Decision:** Retain these observations provisionally. They may be legitimate income values. Consider log transformation or robust scaling and compare model performance later.

### DTI

| Metric | Value |
|---|---:|
| Q1 | 11.28 |
| Q3 | 22.98 |
| IQR | 11.70 |
| Upper IQR bound | 40.53 |
| IQR outliers | 275 |
| Maximum | 9,999 |

Additional investigation:
- 296 observations have DTI greater than 40.
- 35 observations have DTI greater than 50.
- 8 observations have DTI greater than 100.
- The maximum value of 9,999 belongs to a record with annual income of zero and loan status Charged Off.

**Decision:** Treat 9,999 as a suspected anomaly. Consider replacing it with missing and adding an anomaly indicator. Retain other extreme values initially and evaluate their impact.

### Revolving balance

| Metric | Value |
|---|---:|
| Q1 | $6,025 |
| Q3 | $19,620 |
| IQR | $13,595 |
| Upper IQR bound | $40,012.50 |
| Potential outliers | 21,259 |
| Outlier percentage | 5.37% |
| Maximum | $1,743,266 |

**Decision:** Retain observations provisionally. High balances may be genuine. Consider log transformation or robust scaling and validate during modelling.

### Revolving utilisation

| Metric | Value |
|---|---:|
| Q1 | 35.8 |
| Q3 | 72.9 |
| IQR | 37.1 |
| Upper IQR bound | 128.55 |
| IQR outliers | 12 |
| Outlier percentage | Approximately 0.003% |
| Maximum | 892.3 |

Additional investigation:
- 11 observations fall between 129.4 and 153.0.
- One observation has a value of 892.3.
- The 892.3 observation has revolving balance $2,677 and only two open accounts.
- Its implied credit limit is unusually low, but the exact cause is unknown.

**Decision:** Retain the borrower record. Investigate the extreme utilisation value separately. Consider setting 892.3 to missing and imputing using the training-data median. Retain the other high observations initially and validate.

## 4.4 Data types and date inconsistencies

Original date fields were strings in `MMM-YYYY` format.

The following conversions have been performed:

- `issue_d` → datetime
- `earliest_cr_line` → datetime

Pandas assigns the first day of the month during parsing. This is expected because the original values contain month and year, not an exact day.

---

# 5. Feature Assessment

## 5.1 High cardinality

| Feature | Unique values | Proposed treatment | Reason |
|---|---:|---|---|
| emp_title | 173,105 | Exclude from initial model | Very high cardinality; requires aggregation or other encoding strategy. |
| title | 48,816 | Exclude from initial model | High cardinality and overlapping information with purpose. |
| address | 393,700 | Exclude from initial model | Nearly unique per record; potential identifier. |

These are preliminary decisions and can be revisited if a defensible feature-engineering approach is identified.

## 5.2 Correlation and multicollinearity

| Feature pair | Correlation | Proposed action |
|---|---:|---|
| loan_amnt – installment | 0.954 | Investigate redundancy; compare model performance with alternatives. |
| pub_rec – pub_rec_bankruptcies | 0.699 | Retain initially; evaluate multicollinearity. |
| open_acc – total_acc | 0.681 | Retain initially; evaluate contribution and redundancy. |

## 5.3 Potential data leakage

**Status: Pending investigation.**

Before modelling, examine whether any features contain information that would not be available at the intended prediction time.

Particular attention should be paid to:
- Whether all predictors are available at loan application or origination.
- Whether outcome-related or post-origination information is present.
- Whether date fields and loan outcome collection introduce temporal selection bias.

## 5.4 Class imbalance

Charged Off represents 19.61% of the dataset, while Fully Paid represents 80.39%.

Potential considerations:
- Stratified train/test splitting.
- Appropriate evaluation metrics.
- Class weights or resampling, if justified by model performance.

No resampling or class-weighting decision has been made yet.

---

# 6. Feature Engineering Log

| New Feature | Derived From | Transformation / Logic | Status |
|---|---|---|---|
| credit_history_months | issue_d, earliest_cr_line | Difference between origination month and earliest credit-line month, calculated in months. | Created and investigated. |
| credit_history_years | credit_history_months | Months divided by 12. | Created for interpretation; redundant with months. |
| issue_year | issue_d | Extracted calendar year. | Created and investigated. |
| log_annual_inc | annual_inc | Log transformation used during exploratory investigation. | Exploratory; final retention pending. |

## 6.1 Credit history analysis

| Metric | Value |
|---|---:|
| Mean | 189.05 months |
| Median | 172 months |
| Minimum | 36 months |
| Maximum | 842 months |
| Negative values | 0 |

Additional findings:
- Ten longest credit histories range from approximately 61 to 70 years.
- These observations were retained provisionally because their validity cannot be established from the available data.
- Fully Paid borrowers had a mean credit history of approximately 190 months, compared with 184 months for Charged Off borrowers.

**Credit history group analysis:**

| Credit history | Charged Off % |
|---|---:|
| 0–5 years | 23.10 |
| 5–10 years | 21.26 |
| 10–15 years | 20.18 |
| 15–20 years | 18.77 |
| 20–30 years | 17.79 |
| 30–42 years | 17.99 |
| 42+ years | 23.65 |

The default rate generally declines with longer credit history up to 20–30 years, with an increase in the 42+ group. The longest-history group is relatively small.

**Feature decision:** Retain `credit_history_months` for model evaluation. Do not retain both months and years because they represent the same information.

## 6.2 Issue year analysis

| Issue year | Charged Off % | Fully Paid % |
|---|---:|---:|
| 2007 | 16.41 | 83.59 |
| 2008 | 15.81 | 84.19 |
| 2009 | 12.26 | 87.74 |
| 2010 | 13.21 | 86.79 |
| 2011 | 15.25 | 84.75 |
| 2012 | 16.48 | 83.52 |
| 2013 | 15.74 | 84.26 |
| 2014 | 23.11 | 76.89 |
| 2015 | 24.90 | 75.10 |
| 2016 | 13.13 | 86.87 |

Additional findings:
- 2014 and 2015 have the highest charged-off percentages.
- These years also have substantial loan volumes.
- The higher rates persist when 36-month and 60-month loans are analysed separately.
- The lower 2016 rate needs cautious interpretation because the dataset does not provide an explicit outcome-observation or closure date.

**Feature decision:** Retain `issue_year` for further evaluation, subject to temporal validation and leakage assessment.

---

# 7. Data Preparation Decision Log

This is the central record of proposed and approved preprocessing actions.

| ID | Feature / Issue | Observation | Proposed Decision | Reason | Validation Required | Status |
|---|---|---|---|---|---|---|
| D01 | mort_acc | 9.54% missing; median 1 | Median imputation | Numerical count feature with right-skewed distribution | Check distribution after imputation | Pending |
| D02 | emp_title | 173,105 unique values | Exclude from initial model | Very high cardinality | Revisit possible aggregation | Pending |
| D03 | emp_length | 4.62% missing; missing group has higher default rate | Encode missing as Unknown | Missingness may carry information | Compare category distribution and model impact | Pending |
| D04 | title | 48,816 unique values | Exclude from initial model | High cardinality and overlap with purpose | Revisit if useful | Pending |
| D05 | pub_rec_bankruptcies | Median and mode 0; small missing percentage | Median imputation | Low missingness and zero is typical | Check distribution and model impact | Pending |
| D06 | revol_util | 276 missing; median 54.8 | Median imputation | Low missingness | Check distribution after imputation | Pending |
| D07 | annual_inc | Highly skewed; 4.22% IQR outliers | Retain; evaluate log transformation or robust scaling | High values may be legitimate | Compare model performance | Pending |
| D08 | dti | Maximum 9,999; suspicious observation | Investigate 9,999; consider missing replacement and anomaly flag | Extreme value may distort modelling | Compare before/after treatment | Pending |
| D09 | revol_bal | 5.37% IQR outliers | Retain; consider log transformation or robust scaling | Values may be legitimate | Compare model performance | Pending |
| D10 | revol_util = 892.3 | One unusually high observation | Consider setting value to missing and imputing | Implied credit limit is unusually low | Validate sensitivity | Pending |
| D11 | loan_amnt, installment | Correlation 0.954 | Investigate redundancy | Strong relationship between features | Compare alternative model inputs | Pending |
| D12 | grade, sub_grade | Overlapping risk classifications | Evaluate alternative representations | Avoid redundant information | Compare model performance and coefficient stability | Pending |
| D13 | credit_history_months, credit_history_years | Same information in different units | Retain months only | Avoid duplicate representation | Confirm feature inventory | Proposed |
| D14 | issue_year | Different default rates across vintages | Retain provisionally | Potentially useful time-related information | Temporal validation and leakage checks | Pending |
| D15 | emp_title, title, address | Very high cardinality | Exclude from initial model | Avoid uncontrolled encoding and identifier-like features | Reassess only with justification | Pending |
| D16 | application_type | Minority categories have very small sample sizes | Retain initially | Avoid premature removal | Assess model stability | Pending |
| D17 | Target imbalance | 19.61% Charged Off | Use stratified splitting; assess appropriate metrics | Preserve class proportions | Validate split and model metrics | Pending |
| D18 | Date features | Month-year dates parsed to datetime | Retain engineered features as appropriate | Enable meaningful date-based analysis | Leakage assessment | In progress |

**Important:** All proposed numerical imputations, scaling, transformations and encoding parameters must be learned using the training data only, then applied to validation/test data. This prevents data leakage.

---

# 8. Preprocessing Implementation Record

**Status: Not yet started as a formal preprocessing pipeline.**

Completed exploratory preparation:
- Parsed `issue_d` and `earliest_cr_line` into datetime.
- Created `credit_history_months`.
- Created `credit_history_years` for interpretation.
- Created `issue_year`.
- Used a log-transformed annual-income representation for exploratory inspection.

Pending:
- Train/test splitting.
- Missing-value treatment.
- Outlier treatment.
- Categorical encoding.
- Numerical scaling or transformation.
- Feature selection.
- Pipeline construction.

---

# 9. Validation and Comparison

**Status: Pending.**

We will use this section to compare the original data and prepared data, and later evaluate whether preprocessing decisions improve model performance.

| Decision / Transformation | Before | After | Impact | Final Decision |
|---|---|---|---|---|
| Missing-value treatment | | | | |
| DTI anomaly treatment | | | | |
| Revolving utilisation treatment | | | | |
| Annual income transformation | | | | |
| Feature selection | | | | |
| Categorical encoding | | | | |
| Numerical scaling | | | | |

Validation should include:
- Number of records and columns retained.
- Missing-value counts.
- Distribution changes.
- Outlier treatment.
- Train/test consistency.
- Model performance.
- Leakage checks.

---

# 10. Final Feature Inventory

**Status: Pending preprocessing and feature selection.**

| Feature | Original / Derived | Data Type | Treatment Applied | Retain / Exclude | Reason |
|---|---|---|---|---|---|
| loan_amnt | Original | Numerical | Pending | Pending | |
| term | Original | Categorical | Pending | Pending | |
| int_rate | Original | Numerical | Pending | |
| installment | Original | Numerical | Pending | |
| grade | Original | Categorical / Ordinal | Pending | |
| sub_grade | Original | Categorical / Ordinal | Pending | |
| emp_title | Original | Categorical | Pending | |
| emp_length | Original | Categorical | Pending | |
| home_ownership | Original | Categorical | Pending | |
| annual_inc | Original | Numerical | Pending | |
| verification_status | Original | Categorical | Pending | |
| issue_d | Original | Date | Parsed | Pending | |
| loan_status | Target | Categorical / Binary | Encode Charged Off=1, Fully Paid=0 | Retain as target | |
| purpose | Original | Categorical | Pending | Pending | |
| title | Original | Categorical | Pending | |
| dti | Original | Numerical | Pending | |
| earliest_cr_line | Original | Date | Parsed | Pending | |
| open_acc | Original | Numerical | Pending | |
| pub_rec | Original | Numerical | Pending | |
| revol_bal | Original | Numerical | Pending | |
| revol_util | Original | Numerical | Pending | |
| total_acc | Original | Numerical | Pending | |
| initial_list_status | Original | Categorical | Pending | Pending | |
| application_type | Original | Categorical | Pending | Pending | |
| mort_acc | Original | Numerical | Pending | Pending | |
| pub_rec_bankruptcies | Original | Numerical | Pending | Pending | |
| address | Original | High-cardinality / Identifier-like | Pending | Pending | |
| credit_history_months | Derived | Numerical | Created | Pending | |
| credit_history_years | Derived | Numerical | Created | Likely exclude | Redundant with months |
| issue_year | Derived | Numerical / Categorical | Created | Pending | |
| log_annual_inc | Derived | Numerical | Exploratory | Pending | |

---

# 11. Outstanding Questions and Assumptions

Prediction scenario established: pre-approval underwriting.
Post-approval or post-issuance information must not be used as predictors.
Features such as interest rate, installment, grade, sub-grade and verification status require further timing and leakage assessment.

| ID | Question / Assumption | Why It Matters | How to Resolve | Status |
|---|---|---|---|---|
| Q01 | Are there exact duplicate records? | Duplicates may distort analysis and modelling. | Run duplicate checks. | Pending |
| Q02 | Are all predictor fields available at loan application/origination? | Prevent target/data leakage. | Review feature definitions and timing. | Pending |
| Q03 | Why is DTI 9,999 for one borrower? | Could be an anomaly or special data representation. | Investigate source documentation and record. | Pending |
| Q04 | Why is revol_util 892.3? | Implies an unusually low credit limit. | Investigate source and sensitivity. | Pending |
| Q05 | Are the longest credit histories legitimate? | Values reach approximately 70 years. | Review available definitions and data provenance. | Pending |
| Q06 | Do home-ownership categories ANY, NONE and OTHER have sufficient records? | Small groups can produce unstable percentages. | Inspect category counts. | Pending |
| Q07 | Should grade or sub_grade be used? | They represent overlapping information. | Compare model performance. | Pending |
| Q08 | Should loan_amnt or installment be excluded? | Very high correlation. | Compare models and investigate multicollinearity. | Pending |
| Q09 | Why are 2016 default rates lower? | Could reflect differences in borrower mix or outcome observation. | Review dataset collection and outcome definitions. | Pending |
| Q10 | Are minority application types sufficiently represented? | Joint and Direct Pay categories are small. | Evaluate stability and modelling impact. | Pending |
| Q11 | Is there a reliable identifier or loan-level uniqueness constraint? | Helps assess duplicates and record integrity. | Review source schema. | Pending |

---

# 12. Final EDA Summary

The EDA identified several potentially useful predictors of loan repayment outcomes.

**Important associations observed:**
- Grade and sub-grade show strong relationships with charged-off rates.
- Higher interest rates are associated with higher charged-off rates.
- Charged-off borrowers have higher median DTI and revolving utilisation.
- Fully Paid borrowers have higher median annual income and slightly longer credit histories.
- Loan term and issue year show differences in repayment outcomes.
- Missing employment-length information is associated with a higher charged-off rate.

**Important data preparation considerations:**
- Missing values in six features.
- Highly skewed numerical variables.
- Suspicious extreme values in DTI and revolving utilisation.
- High-cardinality categorical features.
- Strong correlation between certain numerical features.
- Potential redundancy between grade and sub-grade.
- Target class imbalance.
- Potential data leakage and temporal observation limitations that require investigation.

**Current conclusion:**

**Address Column Assessment**

- Total records: 396,030
- Missing values: 0
- Unique addresses: 393,700
- Approximate uniqueness: 99.41%

**Observation:** The address column has extremely high cardinality, with almost every record containing a distinct address.

**Decision:** Exclude `address` from the initial predictive model. Direct categorical encoding would create a very large number of features and may introduce unnecessary complexity and privacy concerns.

**Status:** Proposed exclusion; to be confirmed during final feature selection.

The exploratory analysis has provided a sound understanding of the dataset and identified several issues that need to be addressed before modelling.

No final preprocessing decisions or model-performance claims have been made yet. All proposed treatments will be tested and documented before being finalised.

**Next project stage:** Data leakage assessment, duplicate checks and systematic preprocessing planning.

**Home Ownership – Category Assessment**

- MORTGAGE: 198,348 records (50.08%)
- RENT: 159,790 records (40.35%)
- OWN: 37,746 records (9.53%)
- OTHER: 112 records
- NONE: 31 records
- ANY: 3 records

**Findings:** MORTGAGE, RENT and OWN account for almost all observations. OTHER, NONE and ANY have very small sample sizes, making their individual default rates unreliable for comparison.

**Proposed preparation:** Retain the three major categories separately and consider grouping OTHER, NONE and ANY into a single `Other` category.

**Status:** Proposed; implementation and model impact pending.

# Feature Assessment – Data Leakage

**Project prediction scenario:** Default risk prediction during underwriting, before loan approval and issuance.

| Feature | Assessment | Proposed decision | Status |
|---|---|---|---|
| int_rate | Pricing information; may reflect prior risk assessment | Exclude from primary model | Pending implementation |
| installment | Depends on proposed loan terms being calculated | Exclude from early underwriting model | Pending implementation |
| grade | Lender-assigned risk classification | Exclude | Pending implementation |
| sub_grade | More granular lender-assigned risk classification | Exclude | Pending implementation |

## Data Preparation Decision Log

**D19 – Interest Rate**
- Decision: Exclude `int_rate` from the primary model.
- Reason: May not be available at the chosen prediction point.
- Alternative: Evaluate separately in a post-pricing model.

**D20 – Installment**
- Decision: Exclude `installment` from the primary early-underwriting model.
- Reason: Depends on proposed loan terms being calculated.
- Alternative: Consider retaining if the prediction point is after proposed terms are available.

**D21 – Grade**
- Decision: Exclude `grade`.
- Reason: Represents an existing lender's risk assessment.

**D22 – Sub-grade**
- Decision: Exclude `sub_grade`.
- Reason: Represents an existing lender's more granular risk assessment and overlaps with `grade`.

**Implementation status:** All four decisions are proposed. No columns have been physically removed from the original dataset.

**D23 – Verification Status**

- Feature: `verification_status`
- Decision: Exclude from the primary model.
- Reason: Income verification has not begun at our chosen prediction point (immediately after application submission).
- Alternative: Consider retaining in a separate post-verification model.
- Status: Proposed exclusion; implementation pending.

**D24 – Initial Listing Status**

- Feature: `initial_list_status`
- Decision: Exclude from the primary model.
- Reason: Represents how a loan is offered to investors, rather than information available at initial application submission.
- Additional observation: The difference in charged-off rates between fractional and whole loans is relatively small (0.77 percentage points).
- Status: Proposed exclusion; implementation pending.

**D25 – Issue Date and Issue Year**

- Features: `issue_d`, `issue_year`
- Decision: Exclude both from the primary model.
- Reason: These features represent the actual loan issuance date, which is unavailable immediately after application submission.
- Alternative use: Historical trend and loan-vintage analysis.
- Status: Proposed exclusion; implementation pending.

**D26 – Earliest Credit Line**

- Feature: `earliest_cr_line`
- Decision: Retain as a potential predictor.
- Reason: Can be obtained from the applicant's credit bureau report during application assessment.
- Assumption: LoanTap obtains the applicant's credit report at the chosen prediction point.
- Related feature: Recalculate `credit_history_months` using the application date rather than `issue_d`.
- Status: Retention proposed; reference-date correction pending.

**D27 – Residential Address**

- Feature: `address`
- Availability: Potentially available at application submission.
- Decision: Exclude from the primary model.
- Reason: Extremely high cardinality (approximately 99.41% unique), potential model complexity and privacy considerations.
- Alternative: Explore broader geographic features only if justified.
- Status: Proposed exclusion; implementation pending.

  **D28 – Employment Title**

- Feature: `emp_title`
- Availability: Potentially available at application submission.
- Unique values: 173,105.
- Decision: Exclude from the initial model.
- Reason: Extremely high cardinality, inconsistent text descriptions and substantial encoding complexity.
- Additional consideration: Annual income is available as a direct numerical feature. However, this is not proof of multicollinearity.
- Status: Proposed exclusion; implementation pending.

  **D29 – Loan Title**

- Feature: `title`
- Availability: Potentially available at application submission.
- Unique values: 48,816.
- Decision: Exclude from the primary model.
- Reason: High cardinality, applicant-provided free text, possible inconsistency and overlap with the structured `purpose` feature.
- Retained alternative: `purpose` (14 categories).
- Status: Proposed exclusion; implementation pending.

  ### D30. `emp_length` – Employment Length

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes, potentially collected during application |
| Retain or exclude? | Retain |
| Missing values | 18,301 (4.62%) |
| Missing-value treatment | Separate `Unknown` category |
| Remove records? | No |
| Impute with mode? | No |
| Reasoning | Structured feature with manageable categories; missingness may carry predictive information |
| Preprocessing note | Handle missing values during preprocessing; preserve the original dataset |

**EDA observation:** Charged Off rate is approximately 27.53% for records with missing employment length, compared with 19.23% for records with reported employment length. This is an association, not evidence of causation.

### D31. `home_ownership` – Home Ownership Status

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes – applicant-provided information |
| Retain or exclude? | Retain |
| Major categories | MORTGAGE, RENT, OWN |
| Rare categories | OTHER, NONE, ANY |
| Missing values | None reported |
| Proposed preprocessing | Group rare categories into `Other` |
| Encoding | Categorical encoding |
| Reasoning | Relevant applicant information available at application submission |

**EDA observation:** Charged Off rates are 16.96% for MORTGAGE, 22.66% for RENT, and 20.68% for OWN. Interpret these as associations, not causal relationships.

### D32. `annual_inc` / `log_annual_inc` – Annual Income

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes – applicant-provided |
| Raw feature | `annual_inc` |
| Derived feature | `log_annual_inc` |
| Distribution | Highly right-skewed |
| Feature engineering | Log transformation |
| Retain for modelling | `log_annual_inc` |
| Exclude from initial model | Raw `annual_inc` |
| Reasoning | Reduce skewness and avoid duplicate representations |
| Data retention | Preserve original `annual_inc` in raw dataset |

**Preprocessing note:** Use `np.log1p()` to accommodate zero income values. Investigate negative or invalid values before transformation.

### D33. `loan_amnt` – Loan Amount

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes – applicant-requested amount |
| Retain or exclude? | Retain |
| Reasoning | Relevant to the applicant's borrowing requirement and potential repayment risk |
| Preprocessing consideration | Examine distribution and outliers; consider scaling depending on model |


### D34. `term` – Loan Repayment Term

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes – applicant-selected |
| Retain or exclude? | Retain |
| Categories | 36 months, 60 months |
| Most common category | 36 months (76.25%) |
| Reasoning | Influences repayment duration and monthly repayment obligation |
| Preprocessing | Categorical encoding |

**Important:** Although `term` influences `installment`, it is not leakage because the applicant-selected term is available at application submission.

### D35. `purpose` – Loan Purpose

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes – applicant-provided |
| Retain or exclude? | Retain |
| Categories | 14 structured categories |
| Most common category | debt_consolidation (234,507 records) |
| Notable EDA observation | small_business Charged Off rate: 29.45% |
| Reasoning | Provides information about the applicant's borrowing requirement |
| Preprocessing | Categorical encoding; review rare categories |

**Important:** Interpret differences in default rates as associations, not causal relationships. Be cautious with categories having small sample sizes.

### D36. `dti` – Debt-to-Income Ratio

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes, subject to application-stage calculation |
| Retain or exclude? | Retain |
| Mean – Charged Off | 19.66 |
| Mean – Fully Paid | 16.82 |
| Maximum | 9,999 (extreme value) |
| Reasoning | Important indicator of existing debt burden relative to income |
| Preprocessing | Investigate extreme values; determine treatment before modelling |
| Scaling | Consider depending on algorithm |

**Important:** Verify that DTI reflects information available at application submission. Do not automatically remove extreme values without investigation.

### D37. `open_acc` – Number of Open Credit Accounts

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes – assuming application-stage credit bureau access |
| Retain or exclude? | Retain |
| Feature type | Numerical |
| Correlation observation | Correlation with `total_acc`: approximately 0.68 |
| Reasoning | Indicates existing credit exposure and borrowing activity |
| Preprocessing | Examine distribution and unusual values; consider scaling |
| Further analysis | Assess relationship with `total_acc` |

**Important:** Correlation with another feature does not automatically justify exclusion.

### D38. `total_acc` – Total Number of Credit Accounts

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes – assuming application-stage credit bureau access |
| Retain or exclude? | Retain |
| Feature type | Numerical |
| Correlation with `open_acc` | Approximately 0.68 |
| Correlation with `mort_acc` | Approximately 0.38 |
| Reasoning | Provides a broader view of the applicant's credit history |
| Preprocessing | Examine distribution and unusual values; consider scaling |
| Further analysis | Evaluate relationship with `open_acc` |

**Important:** Retain both `open_acc` and `total_acc` initially. Evaluate their individual contribution during modelling.

### D39. `pub_rec` – Derogatory Public Records

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes – assuming application-stage credit bureau access |
| Retain or exclude? | Retain |
| Feature type | Numerical count |
| Most common value | 0 |
| Correlation with `pub_rec_bankruptcies` | Approximately 0.70 |
| Reasoning | Indicates adverse public credit history |
| Preprocessing | Examine distribution and non-zero values; consider scaling |
| Further analysis | Evaluate relationship with `pub_rec_bankruptcies` |

**Important:** Assess correlated credit-history features during modelling rather than automatically removing one.

### D40. `pub_rec_bankruptcies` – Public Record Bankruptcies

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes – assuming application-stage credit bureau access |
| Retain or exclude? | Retain |
| Feature type | Numerical count |
| Missing values | 535 (0.14%) |
| Proposed imputation | 0 |
| Reasoning | Avoid assigning an arbitrary positive bankruptcy count |
| Correlation with `pub_rec` | Approximately 0.70 |
| Multicollinearity treatment | Retain both initially; evaluate using VIF and model behaviour |

**Assumption and limitation:** Missing values are treated as zero for the initial modelling exercise. This assumes missing means no reported bankruptcies, which must be validated against the source data definition where possible.

**Preprocessing note:** Fit the imputation strategy using training data only.

### D41. `mort_acc` – Number of Mortgage Accounts

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes – assuming application-stage credit bureau access |
| Retain or exclude? | Retain |
| Feature type | Numerical count |
| Missing values | 37,795 (9.54%) |
| Correlation with `total_acc` | Approximately 0.38 |
| Reasoning | Provides additional information about mortgage and credit history |
| Missing-value treatment | To be determined during preprocessing |
| Preprocessing | Examine distribution, missingness and relationship with `total_acc` |

**Important:** Do not automatically assume missing mortgage account information means zero mortgage accounts.

### D42. `revol_bal` – Revolving Credit Balance

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes – assuming application-stage credit bureau access |
| Retain or exclude? | Retain |
| Feature type | Numerical |
| EDA observation | Extreme values present |
| Reasoning | Indicates existing revolving debt and financial obligations |
| Preprocessing | Investigate outliers; consider log transformation and scaling |

**Important:** Do not automatically remove extreme values. Preserve the raw feature and perform transformations during preprocessing.

### D43. `revol_util` – Revolving Credit Utilisation

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes – assuming application-stage credit bureau access |
| Retain or exclude? | Retain |
| Feature type | Numerical percentage |
| Maximum observed value | 892.3 (suspicious) |
| Missing values | 276 (0.07%) |
| Reasoning | Indicates utilisation of available revolving credit |
| Preprocessing | Investigate values above 100%; handle missing values; consider scaling |

**Important:** Do not automatically cap or remove extreme values without investigation. Preserve the original feature and document any treatment.

### D44. `application_type` – Application Type

| Assessment | Decision / Observation |
|---|---|
| Available at prediction time? | Yes |
| Retain or exclude? | Retain |
| Categories | Individual, Joint |
| Feature type | Categorical |
| Reasoning | Provides context about the structure of the loan application |
| Preprocessing | Categorical encoding |


# D45. Derived Credit History Features – Prediction-Time Availability Assessment

### Features assessed

- `credit_history_months`
- `credit_history_years`
- `credit_history_group`
- `earliest_cr_line`

| Assessment | Decision / Observation |
|---|---|
| Original date feature | `earliest_cr_line` |
| Reference date used for derivation | `issue_d` |
| Derived features | `credit_history_months`, `credit_history_years`, `credit_history_group` |
| Prediction point | Immediately after application submission |
| Are derived features available at prediction time? | No – derived using actual issuance date |
| Retain derived features? | No – exclude from primary model |
| Retain `earliest_cr_line`? | Yes, in raw dataset |
| Missing application date? | Yes – not available in the dataset |
| Alternative derivation | Application date minus earliest credit line date |
| Current limitation | Cannot accurately calculate credit-history duration at application submission |

### Decision and reasoning

The three derived features are excluded because they use `issue_d`, which represents the actual loan issuance date and is unavailable at the intended prediction point.

`earliest_cr_line` is retained because it could be available through the applicant's credit bureau report during application assessment.

However, without the actual application date, we cannot calculate a valid credit-history duration for the intended prediction scenario.

### Future consideration

If application dates become available, derive credit-history duration using:

`application_date - earliest_cr_line`

**Documentation note:** Preserve the original date and derived columns in the raw dataset. Exclude the three derived features only from the primary model's predictor list.

# D46. Final Feature Decision Register

## A. Prediction scenario

| Item | Decision |
|---|---|
| Prediction point | Immediately after application submission |
| Before underwriting? | Yes |
| Before verification? | Yes |
| Before approval and issuance? | Yes |
| Applicant-provided information | Assumed available |
| Credit bureau information | Assumed available during application assessment |
| Application date | Not available in dataset |

## B. Final feature classification

| Classification | Features |
|---|---|
| Retain as predictors | `loan_amnt`, `term`, `emp_length`, `home_ownership`, `log_annual_inc`, `purpose`, `dti`, `open_acc`, `total_acc`, `pub_rec`, `pub_rec_bankruptcies`, `mort_acc`, `revol_bal`, `revol_util`, `application_type` |
| Exclude from primary model | `int_rate`, `installment`, `grade`, `sub_grade`, `verification_status`, `initial_list_status`, `emp_title`, `title`, `address`, `annual_inc`, `issue_year`, `credit_history_months`, `credit_history_years`, `credit_history_group` |
| Target variable | `loan_status` |
| EDA/reference only | `issue_d`, `issue_year`, `earliest_cr_line`, raw `annual_inc` and excluded text/address columns |

## C. Important preprocessing decisions

| Feature | Planned treatment |
|---|---|
| `emp_length` | Missing → `Unknown` |
| `home_ownership` | Group rare categories into `Other` |
| `annual_inc` | Use `log_annual_inc`; exclude raw version from predictors |
| `dti` | Investigate extreme values |
| `pub_rec_bankruptcies` | Proposed missing-value imputation with 0; validate assumption |
| `mort_acc` | Investigate missingness; treatment pending |
| `revol_bal` | Investigate extreme values; consider log transformation |
| `revol_util` | Investigate values above 100%; handle missing values |
| `pub_rec` and `pub_rec_bankruptcies` | Evaluate multicollinearity |
| `open_acc` and `total_acc` | Evaluate correlation and individual contribution |

## D. Target encoding

| Target value | Encoded value |
|---|---:|
| Fully Paid | 0 |
| Charged Off | 1 |

## E. Data preparation principles

- Preserve original raw data.
- Create a separate modelling dataset.
- Separate target and predictors.
- Fit imputation, encoding and scaling transformations on training data only.
- Investigate outliers before treatment.
- Evaluate multicollinearity where relevant.
- Keep the test set untouched until final evaluation.

## F. Outstanding limitations

1. Application date is unavailable.
2. Credit bureau availability at application assessment is an assumption.
3. Zero imputation for missing `pub_rec_bankruptcies` requires validation.
4. Missing-value treatment for `mort_acc` remains pending.
5. Extreme values in `dti`, `revol_bal` and `revol_util` require further investigation.

**Status:** Feature availability and leakage assessment completed. Ready to begin preprocessing.


