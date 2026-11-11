# LoanTap – Personal Loan Default Prediction

## 1. Business Problem

LoanTap aims to develop a machine learning model to assess the creditworthiness of individuals applying for personal loans.

The objective is to predict whether a borrower is likely to fully repay the loan or default (Charged Off), based on their financial, employment and credit-related attributes.

This is a binary classification problem.

The model should help LoanTap:
- Identify potentially risky borrowers.
- Reduce financial losses arising from loan defaults.
- Avoid rejecting creditworthy applicants.
- Support informed lending and risk-management decisions.

## 2. Initial Dataset Exploration

The dataset contains 396,030 records and 27 attributes.

The target variable is `loan_status`, which contains two categories:

- Fully Paid: 318,357 records (80.39%).
- Charged Off: 77,673 records (19.61%).

The target variable is imbalanced, with approximately 80% of observations belonging to the Fully Paid category.

Six features contain missing values. `mort_acc` has the highest missing percentage at 9.54%.

The dataset contains both numerical and categorical variables. Some categorical variables, particularly `emp_title`, `title` and `address`, have high cardinality and require further investigation before encoding.

## 3. Initial Observations

1. The dataset contains a substantial number of historical loan records.
2. Approximately one in five borrowers has a Charged Off loan status.
3. Missing values need to be investigated and treated before model building.
4. Class imbalance makes recall, precision and ROC-AUC important evaluation metrics.
5. Date-related and high-cardinality variables require appropriate feature engineering and preprocessing.

## 4. Univariate Analysis – Numerical Variables

### Continuous Variables

- Annual income exhibits significant positive skewness, indicating that a small proportion of borrowers report exceptionally high incomes.
- DTI and revolving balance also exhibit substantial positive skewness.
- Several variables contain potential outliers, particularly annual income, revolving balance, installment and DTI.
- These observations require further investigation before deciding on outlier treatment.

### Count-based Variables

- The majority of borrowers have zero public derogatory records.
- Public records are concentrated around zero, with fewer borrowers having multiple derogatory records.
- This variable may provide useful information for assessing borrower creditworthiness.

### Key Observations

The numerical variables exhibit different distributions and ranges. Several financial attributes are positively skewed and contain extreme observations. Median-based statistics may therefore be more representative than mean-based statistics for certain variables.

Outliers should not be removed blindly because extreme financial values may represent genuine borrower characteristics.

## 5. Univariate Analysis – Categorical Variables

The categorical variable analysis reveals the following:

1. **Loan Term:** 36-month loans account for approximately 76.26% of the dataset, indicating a preference for shorter loan tenures.

2. **Loan Grade:** Grade B is the most frequently assigned grade, followed by grades C and A.

3. **Sub-grade:** B3 is the most common sub-grade.

4. **Employment Length:** Borrowers with 10 or more years of employment form the largest employment-length category.

5. **Home Ownership:** MORTGAGE is the most common home ownership category, representing approximately 50.08% of borrowers. Home ownership provides additional information about the borrower's financial circumstances and potential existing housing obligations.

6. **Verification Status:** Verified is the most frequent individual category, followed by Source Verified and Not Verified.

7. **Loan Purpose:** Debt consolidation is the most common loan purpose, accounting for approximately 59.21% of records. Credit card repayment is the second most common purpose.

8. **Initial Listing Status:** The F category is more common than W.

9. **Application Type:** Individual applications dominate the dataset, representing approximately 99.82% of records.

### Overall Inference

The dataset primarily consists of individual borrowers with mortgage-based home ownership, relatively long employment histories and 36-month loan terms. Debt consolidation is the predominant reason for borrowing.

## 6. Bivariate Analysis 

### Loan Grade vs Loan Status

A cross-tabulation was performed to examine the relationship between LoanTap-assigned grades and loan repayment outcomes.

The analysis reveals a clear trend:

- Grade A has the highest repayment rate of 93.71% and the lowest charge-off rate of 6.29%.
- Grade G has the lowest repayment rate of 52.16% and the highest charge-off rate of 47.84%.
- The charge-off rate consistently increases as the loan grade moves from A to G.

**Inference:**

Loan grade demonstrates a strong association with loan repayment behaviour. Borrowers assigned better grades have historically shown higher repayment rates, while borrowers with lower grades have demonstrated higher default risk.

This indicates that LoanTap's internal grading system may be an important indicator of creditworthiness.

**Business Recommendation:**

LoanTap can use loan grades as one of the inputs in its credit risk assessment process. Applicants with lower grades may require additional scrutiny, stricter eligibility criteria or more conservative lending terms.

However, loan grade should not be the sole determinant of loan approval.

### Interest Rate vs Loan Status

The relationship between interest rate and loan repayment status was examined using box plots and descriptive statistics.

The findings are:

- Borrowers whose loans were Charged Off have a mean interest rate of 15.88%, compared with 13.09% for Fully Paid borrowers.
- The median interest rate for Charged Off borrowers is 15.61%, compared with 12.99% for Fully Paid borrowers.
- The difference in mean interest rates is 2.79 percentage points.

**Inference:**

Borrowers with Charged Off loans generally have higher interest rates than borrowers who fully repaid their loans. This suggests a positive association between interest rates and credit risk.

Higher interest rates may reflect the lender's assessment of borrower risk and may also increase the repayment burden.

However, the distributions overlap, indicating that interest rate alone cannot accurately distinguish between defaulters and non-defaulters.

**Business Recommendation:**

Interest rate should be considered alongside other borrower characteristics, such as income, DTI, loan amount and credit history, when assessing creditworthiness.

### Relationship Between Loan Amount and Monthly Installment

The scatterplot indicates a strong positive linear relationship between loan amount and monthly installment. The correlation coefficient is **0.9539**, suggesting that borrowers with higher loan amounts generally have higher monthly repayment obligations.

This relationship is expected because the monthly installment is calculated based on the loan amount, interest rate and repayment tenure.

**Inference:** Loan amount and monthly installment contain overlapping information. Since they are highly correlated, their combined use in Logistic Regression may introduce multicollinearity. This will be considered during feature selection and model building.

### Relationship Between Annual Income and Loan Status

The boxplot and summary statistics indicate that borrowers who fully paid their loans generally had higher annual incomes than borrowers who were charged off.

The median annual income for Fully Paid borrowers was $65,000, compared with $59,000 for Charged Off borrowers. The mean income was also higher for Fully Paid borrowers ($75,829.95) than for Charged Off borrowers ($67,535.54).

The boxplot shows several extreme income outliers in both groups. These values compress the central portion of the plot and make direct visual comparison difficult.

**Inference:** Annual income may be a useful predictor of loan repayment behaviour. Borrowers with higher incomes appear to have a somewhat lower incidence of charged-off loans. However, this relationship needs to be evaluated alongside other borrower and loan characteristics.

### Relationship Between Debt-to-Income Ratio (DTI) and Loan Status

The analysis indicates that borrowers who were charged off had higher DTI values than borrowers who fully paid their loans.

- Median DTI for Charged Off borrowers: 19.34
- Median DTI for Fully Paid borrowers: 16.34
- Mean DTI for Charged Off borrowers: 19.66
- Mean DTI for Fully Paid borrowers: 16.82

The maximum DTI values are unusually high in both groups, suggesting the presence of extreme observations that require further investigation.

**Inference:** DTI may be a useful predictor of loan repayment behaviour. Borrowers with higher debt obligations relative to their income appear to have a higher incidence of charged-off loans. This is an association and does not establish causation.

### Investigation of Extreme DTI Values

The DTI distribution contains extreme observations.

The 99th percentile is 36.43, and the 99.9th percentile is 39.86, whereas the maximum value is 9,999. This indicates an exceptionally large gap between the majority of observations and the maximum value.

Further investigation revealed that the record with DTI = 9,999 has an annual income of zero. Other unusually high DTI observations were also associated with very low annual incomes.

These observations may represent data-quality issues or unusual borrower circumstances. They require further investigation before deciding on an appropriate treatment.

**Preliminary inference:** DTI is potentially an informative predictor of loan default, but its extreme values may distort statistical summaries and affect model performance. The treatment of these observations will be addressed during data preprocessing.

### Analysis of Extreme DTI Observations

The investigation showed that extreme DTI values are very rare in the dataset.

- 296 records (0.0747%) have DTI above 40.
- 35 records (0.0088%) have DTI above 50.
- Only 8 records (0.0020%) have DTI above 100.

The record count indicates that extreme DTI values affect a very small proportion of the dataset.

**Decision:** These observations will not be removed at this stage. Their treatment will be evaluated during data preprocessing. The original DTI values will be retained so that the impact of any cleaning or outlier treatment can be assessed.

### Relationship Between Revolving Credit Utilisation and Loan Status

The analysis indicates that borrowers who were charged off had higher revolving credit utilisation than borrowers who fully paid their loans.

- Median utilisation for Charged Off borrowers: 59.3%
- Median utilisation for Fully Paid borrowers: 53.7%
- Mean utilisation for Charged Off borrowers: 57.87%
- Mean utilisation for Fully Paid borrowers: 52.80%

Both groups contain observations with utilisation above 100%. The maximum value of 892.3% in the Fully Paid group is particularly unusual and requires further investigation.

**Inference:** Revolving credit utilisation may be a useful predictor of loan repayment behaviour. Borrowers with higher utilisation appear to have a greater incidence of charged-off loans. However, the relationship is associative and does not establish causation.

### Relationship Between Home Ownership and Loan Status

The analysis indicates differences in loan repayment patterns across home ownership categories.

- RENT has the highest Charged Off percentage at 22.66%.
- NONE has a Charged Off percentage of 22.58%.
- OWN has a Charged Off percentage of 20.68%.
- MORTGAGE has a relatively lower Charged Off percentage of 16.96%.
- ANY shows 100% Fully Paid, but its sample size needs to be checked.

**Inference:** Home ownership may be a useful predictor of loan repayment behaviour. However, the differences across categories should be interpreted alongside the number of observations in each category and other borrower characteristics. Home ownership alone is not sufficient to assess credit risk.

### Relationship Between Loan Purpose and Loan Status

The analysis indicates differences in repayment patterns across loan-purpose categories.

- Small business loans have the highest Charged Off percentage at 29.45%.
- Moving and renewable energy loans have charged-off percentages of 23.48% and 23.40%, respectively.
- Wedding loans have the lowest Charged Off percentage at 12.09%.
- Debt consolidation loans have a Charged Off percentage of 20.74%.

**Inference:** Loan purpose may provide useful information for assessing credit risk. Small business loans show a comparatively higher incidence of charged-off loans, while wedding and car loans show lower incidences in this dataset.

These findings represent associations and should be interpreted alongside category sample sizes and other borrower characteristics.


### Loan Purpose – Category Distribution

The loan-purpose distribution is highly imbalanced.

Debt consolidation is the most common purpose, accounting for 234,507 loans (approximately 59.2% of the dataset), followed by credit card loans with 83,019 records.

Small business loans have a relatively high charged-off rate of 29.45%, based on 5,701 records. Renewable energy and educational loans have much smaller sample sizes of 329 and 257, respectively, so their repayment percentages should be interpreted cautiously.

**Inference:** Loan purpose may be useful for predicting repayment behaviour. However, both the default rate and the number of loans in each category should be considered when evaluating business risk.

### Relationship Between Employment Length and Loan Status

The analysis shows relatively small differences in charged-off percentages across employment-length categories.

- Borrowers with less than one year of employment have the highest charged-off percentage at 20.69%.
- Borrowers with 10+ years of employment have the lowest charged-off percentage at 18.42%.
- The charged-off percentage does not decrease consistently as employment length increases.

**Inference:** Employment length may provide some predictive information about loan repayment behaviour. However, the differences across categories are relatively small, suggesting that employment length alone may not be a strong predictor of default. It should be evaluated alongside other financial and borrower characteristics.

### Correlation Analysis of Numerical Features

A correlation analysis was performed to identify relationships among numerical variables and investigate potential multicollinearity.

The following highly correlated pairs were identified:

| Feature Pair | Correlation |
|---|---:|
| loan_amnt and installment | 0.9539 |
| pub_rec and pub_rec_bankruptcies | 0.6994 |
| open_acc and total_acc | 0.6807 |

The strongest relationship was observed between loan amount and installment, indicating substantial overlap in the information captured by these variables.

Other moderately correlated relationships included loan amount and annual income (0.3369), installment and annual income (0.3304), and total accounts and mortgage accounts (0.3811).

**Inference:** Potential multicollinearity exists among some numerical predictors. These relationships will be investigated further during feature selection and VIF analysis. Highly correlated features will not be removed automatically, as their individual predictive value and business relevance must also be considered.

### Categorical Feature Cardinality Analysis

The categorical variables were examined to identify their unique-value counts and determine appropriate encoding strategies.

**Low-cardinality features:** Variables such as term, grade, emp_length, home_ownership, verification_status, purpose, initial_list_status and application_type have a manageable number of categories and are candidates for One-Hot Encoding.

**High-cardinality features:** emp_title (173,105 unique values), title (48,816 unique values) and address (393,700 unique values) have very high cardinality. Direct One-Hot Encoding would create a large number of features and increase model complexity. These variables will be excluded from the initial model or investigated for meaningful aggregation.

**Date features:** issue_d and earliest_cr_line will be converted into date formats and examined for feature engineering opportunities, such as loan issue period and credit history length.

**Target variable:** loan_status will be converted into a binary variable, with Charged Off represented as 1 and Fully Paid as 0.

**Inference:** Appropriate encoding and feature engineering are essential to prepare the dataset for Logistic Regression while controlling dimensionality and avoiding unnecessary model complexity.

### Missing Value Analysis

Missing values were identified in six features.

| Feature | Missing Count | Missing Percentage |
|---|---:|---:|
| mort_acc | 37,795 | 9.54% |
| emp_title | 22,927 | 5.79% |
| emp_length | 18,301 | 4.62% |
| title | 1,756 | 0.44% |
| pub_rec_bankruptcies | 535 | 0.14% |
| revol_util | 276 | 0.07% |

The highest proportion of missing values was observed in mort_acc, followed by emp_title and emp_length.

**Preliminary treatment strategy:**

- mort_acc: Evaluate median imputation and related account information.
- emp_title: Exclude from the initial model due to high cardinality.
- emp_length: Retain missing information as an Unknown category or evaluate mode imputation.
- title: Exclude from the initial model due to high cardinality and potential overlap with loan purpose.
- pub_rec_bankruptcies: Evaluate median or mode imputation.
- revol_util: Evaluate median imputation.

**Inference:** Missing-value treatment will be selected based on feature type, business meaning and distribution. Missing values will not automatically be replaced with zero.

### Investigation of Missing Values – `mort_acc`

- `mort_acc` has 37,795 missing values (9.54%).
- Its correlation with `total_acc` is approximately **0.381**, indicating a moderate positive relationship.
- Group-wise medians show that borrowers with fewer total credit accounts generally have a median of zero mortgage accounts, while the median increases to one for some higher `total_acc` groups.
- However, `total_acc` alone does not reliably determine the number of mortgage accounts.

**Preliminary decision:** Consider median imputation for `mort_acc`. The final imputation value will be confirmed after examining its overall distribution. Imputation parameters will be calculated using the training data only during model preprocessing.

### Missing Value Treatment – `mort_acc`

**Missing values:** 37,795 (9.54%)

**Investigation:**
- The median number of mortgage accounts is 1.
- The mode is 0, indicating that zero mortgage accounts is the most frequently observed value.
- The distribution is right-skewed, with fewer borrowers having a large number of mortgage accounts.
- `mort_acc` has a moderate positive correlation (0.381) with `total_acc`, but this relationship is not sufficiently strong to reliably estimate missing values from total accounts.

**Decision:** Use median imputation for missing `mort_acc` values. The median is preferred over the mode because it is less influenced by the skewed distribution and extreme values.

**Implementation:** Calculate the imputation value from the training data only during model preprocessing.

### Missing Value Treatment – `emp_length`

**Missing values:** 18,301 (4.62%)

**Investigation:**

The relationship between missing employment length and loan repayment status was examined.

| Employment length | Charged Off | Fully Paid |
|---|---:|---:|
| Available | 19.23% | 80.77% |
| Missing | 27.53% | 72.47% |

Borrowers with missing employment length have a higher charge-off rate (27.53%) than borrowers with available employment information (19.23%).

**Decision:** Retain missing employment length as a separate `Unknown` category rather than imputing it with the mode.

**Reason:** Missingness itself may contain predictive information. Replacing missing values with an actual employment-length category could misrepresent the borrower's employment history.

**Implementation:** Convert missing values to `Unknown` during preprocessing. Evaluate its contribution to model performance later.

### Missing Value Treatment – `pub_rec_bankruptcies`

**Missing values:** 535 (0.14%)

**Investigation:**
- The median and mode are both 0.
- Approximately 88.5% of borrowers have zero public record bankruptcies.
- The distribution is strongly right-skewed.
- The correlation with `pub_rec` is approximately 0.699, indicating a fairly strong positive relationship.

**Decision:** Use median imputation (0) for missing values.

**Reason:** The median is robust to the right-skewed distribution, and the percentage of missing values is very small.

**Additional consideration:** Retain both `pub_rec` and `pub_rec_bankruptcies` initially because they represent related but distinct credit-history information. Evaluate potential multicollinearity during modelling.

### Missing Value Treatment – `revol_util`

**Missing values:** 276 (0.07%)

**Investigation:**
- Median: 54.8
- Mean: 53.79
- Mode: 0
- 99th percentile: 98
- 99.9th percentile: 102
- Maximum: 892.3

The distribution has a small number of unusually high values. Values above 100% are unusual but should not automatically be classified as invalid.

**Decision:** Use median imputation (54.8) for missing values.

**Reason:** Median imputation is robust to extreme observations and is preferable to mode imputation in this case.

**Additional consideration:** Investigate extreme values separately during outlier treatment. Do not remove or cap them without evaluating their impact.

### Outlier Investigation – `annual_inc`

**IQR analysis:**

| Statistic | Value |
|---|---:|
| Q1 | 45,000 |
| Q3 | 90,000 |
| IQR | 45,000 |
| Lower bound | -22,500 |
| Upper bound | 157,500 |
| Potential outliers | 16,700 |
| Outlier percentage | 4.22% |

**Further investigation:**

The potential outliers have:
- Median annual income of 199,350.
- 75th percentile of 245,000.
- 99th percentile of 735,066.
- Maximum annual income of 8,706,582.

Most observations above the IQR threshold may represent legitimate high-income borrowers. However, the maximum value is exceptionally high and warrants further inspection.

**Preliminary decision:** Do not delete or automatically cap the outliers. Investigate extreme observations and consider logarithmic transformation or robust scaling during preprocessing.

**Reason:** Income is an important indicator of repayment capacity, and removing legitimate high-income borrowers could result in information loss.

### Final Outlier Assessment – `annual_inc`

The IQR method identified 16,700 potential outliers (4.22%), with an upper bound of 157,500.

Further investigation identified 65 borrowers with annual incomes above 1 million, representing approximately 0.0164% of the dataset.

Their repayment distribution was:
- Fully Paid: 57 (87.69%)
- Charged Off: 8 (12.31%)

The charge-off rate in this small group is lower than the overall dataset charge-off rate of 19.61%. However, the sample size is too small to draw strong conclusions.

**Decision:** Retain the high-income observations without deletion or arbitrary capping. Consider logarithmic transformation or robust scaling during preprocessing and evaluate the impact on model performance.

### Outlier Investigation – `dti`

**IQR analysis:**

| Statistic | Value |
|---|---:|
| Q1 | 11.28 |
| Q3 | 22.98 |
| IQR | 11.70 |
| Lower bound | -6.27 |
| Upper bound | 40.53 |
| Potential outliers | 275 |
| Outlier percentage | 0.0694% |

**Further investigation:**

- The 99th percentile is 36.43, and the 99.9th percentile is 39.86.
- The maximum DTI is 9,999, which is substantially higher than the rest of the distribution.
- Eight observations have DTI above 100.
- Some extreme observations have very low reported annual income.
- The observation with DTI 9,999 also has annual income of zero, suggesting a possible data-quality or calculation issue.

**Preliminary decision:** Do not automatically delete all IQR outliers. Retain potentially meaningful high-DTI observations while separately investigating extreme values, particularly those above 100.

**Further action:** Examine records with zero annual income and evaluate an appropriate treatment for implausible DTI values during preprocessing.

### DTI – Additional Data Quality Investigation

Further investigation identified exactly one borrower with zero annual income. This borrower has a DTI of 9,999 and a loan amount of 3,700.

The distribution of extreme DTI observations is:

| DTI threshold | Count |
|---|---:|
| Above 40.53 | 275 |
| Above 50 | 35 |
| Above 100 | 8 |
| Above 500 | 2 |
| Above 1,000 | 2 |

**Interpretation:**

The number of observations decreases substantially at higher DTI thresholds. The DTI value of 9,999, associated with zero annual income, is particularly suspicious and may represent a data-quality or placeholder value.

**Preliminary decision:** Investigate all eight observations above 100 individually. Consider treating the 9,999 value as missing, retaining an anomaly indicator, and imputing it during model preprocessing. Do not automatically remove all high-DTI observations.

### Final Outlier Assessment – `dti`

The IQR method identified 275 potential outliers (0.0694%), with an upper bound of 40.53.

Further investigation revealed eight observations with DTI above 100.

Of these:
- One borrower has DTI = 9,999 and annual income = 0.
- One borrower has DTI = 1,622 and annual income = 600.
- Six other borrowers have DTI values between approximately 107 and 381.
- Seven of the eight borrowers are Fully Paid.

**Interpretation:**

The extreme values are unusual, particularly when considered alongside the reported annual incomes. However, the repayment outcomes show that high DTI values do not automatically imply loan default.

**Decision:**
- Treat DTI = 9,999 as a suspected anomalous value. Replace it with missing during preprocessing and retain an anomaly indicator.
- Preserve the remaining seven observations initially.
- Retain the other IQR outliers rather than automatically deleting or capping them.
- Evaluate the effect of extreme values on model performance during modelling.

The original dataset will remain unchanged, with these treatments applied through preprocessing.

### Outlier Analysis: Revolving Balance (`revol_bal`)

**Objective:** Identify unusually high revolving balances and determine whether they require treatment before modelling.

**IQR analysis:**
- Q1: 6,025
- Q3: 19,620
- IQR: 13,595
- Lower bound: -14,367.50
- Upper bound: 40,012.50
- Potential outliers: 21,259 (5.37% of the dataset)

**Distribution of potential outliers:**
- Median: 52,501
- Mean: 71,241
- 95th percentile: 160,993
- 99th percentile: 303,498
- Maximum: 1,743,266

**Observations:**

The revolving balance distribution is strongly right-skewed. The mean of the potential outliers is considerably higher than their median, indicating that a small number of exceptionally large balances influence the average.

The highest observations are unusually large but cannot be assumed to be erroneous without further evidence.

**Preliminary decision:**

- Retain the observations; do not automatically delete or cap them.
- Consider a `log1p` transformation during feature engineering.
- Evaluate the effect of transformation on model performance.
- Reassess the feature during model validation.

**Conclusion:** The IQR method identifies statistically unusual revolving balances, but these values may represent genuine borrower financial profiles. Further investigation and model-based evaluation are required before deciding on treatment.

### Outlier Analysis – Revolving Credit Features

**1. Revolving Balance (`revol_bal`)**

- IQR upper limit: 40,012.50
- Number of outliers: 21,259
- Percentage of dataset: 5.37%
- Maximum value: 1,743,266

The feature has a strongly right-skewed distribution, with a small proportion of borrowers reporting exceptionally high revolving balances.

**Decision:** Retain the observations initially. High revolving balances may represent genuine borrower characteristics. Consider transformations or robust scaling during preprocessing and evaluate their impact on model performance.

**2. Revolving Utilisation (`revol_util`)**

- IQR upper limit: 128.55
- Number of outliers: 12
- Percentage of dataset: approximately 0.003%
- Maximum value: 892.3

Twelve observations exceed the IQR upper limit. Eleven values range from 129.4 to 153.0, while one observation has an unusually high value of 892.3.

**Decision:** Retain the observations provisionally and investigate the exceptionally high value separately. Do not automatically delete or cap these records based only on the IQR method.

**Overall conclusion:** Statistical outliers are not necessarily invalid records. Final treatment will be determined during preprocessing, considering data quality, business context and model performance.

### Investigation of Extreme Revolving Utilisation Value

One observation (index 137211) was identified with a revolving utilisation of 892.3%, significantly higher than the other identified outliers.

On examining the complete record:
- Revolving balance: $2,677
- Annual income: $45,000
- Loan amount: $3,500
- Number of open accounts: 2
- Loan status: Fully Paid

The reported revolving balance and utilisation imply an unusually low revolving credit limit of approximately $300.

This value is therefore suspected to be a data-quality or reporting anomaly. However, the actual cause cannot be confirmed from the available dataset.

**Decision:** Retain the borrower record but flag the extreme `revol_util` value for special treatment during preprocessing. Consider replacing the value with missing and applying median imputation using training data only. Evaluate the impact on model performance before finalising the decision.

### Additional Investigation – Revolving Utilisation Outliers

Further examination of the 12 observations exceeding the IQR upper limit of 128.55% revealed:

- Median utilisation: 145.95%
- Maximum utilisation: 892.3%
- Median revolving balance: $13,765
- Maximum revolving balance: $27,233

Eleven observations have utilisation values between 129.4% and 153.0%. One observation, at index 137211, has an exceptionally high utilisation of 892.3%, despite a revolving balance of only $2,677.

This suggests that the 892.3% observation may be an isolated reporting or data-quality anomaly.

**Final preliminary decision:**
- Retain all borrower records.
- Retain `revol_bal` observations and evaluate transformations or robust scaling.
- Flag the 892.3% `revol_util` observation for special treatment.
- If treated as missing, apply median imputation using training data only.
- Validate the decision using model performance.

### Date Conversion and Credit History Feature Engineering

The columns `issue_d` and `earliest_cr_line` were initially stored as strings in the format `MMM-YYYY`.

Both columns were converted to datetime format using the `%b-%Y` format.

A new feature, `credit_history_months`, was derived by calculating the difference between the loan issue date and the earliest reported credit line date.

An additional feature, `credit_history_years`, was created by converting the month difference into years.

**Summary of credit history:**
- Number of records: 396,030
- Average credit history: 189.05 months (15.8 years)
- Median credit history: 172 months (14.3 years)
- Minimum: 36 months (3 years)
- Maximum: 842 months (approximately 70.2 years)
- Negative credit history records: 0

**Preliminary observation:** Borrowers have an average credit history of approximately 15.8 years. The maximum value of 842 months is unusually high and requires further investigation before finalising the feature.

**Potential modelling relevance:** Credit history length may provide information about a borrower's credit experience and could help explain differences in loan repayment outcomes.

### Investigation of Long Credit Histories

The 10 borrowers with the longest calculated credit histories were examined.

Their credit histories ranged from approximately 61 to 70 years, with earliest reported credit lines dating between 1944 and 1955.

These values are unusually high. However, the dataset does not contain borrower age or date of birth, so their validity cannot be independently established.

The records also contain both Fully Paid and Charged Off loans, indicating that long credit history alone does not determine repayment outcome.

**Decision:**
- Retain these observations provisionally.
- Do not apply arbitrary capping or deletion.
- Evaluate the feature during model development.
- Retain `credit_history_months` as the primary feature and exclude the redundant `credit_history_years` feature from the final predictor set.

**Conclusion:** Credit history length is a potentially useful predictor, but its actual contribution to default prediction must be established through model evaluation.

### Bivariate Analysis – Credit History and Loan Status

Credit history length was compared against loan repayment outcomes.

**Credit history by loan status:**

| Statistic | Charged Off | Fully Paid |
|---|---:|---:|
| Mean (months) | 184.34 | 190.20 |
| Median (months) | 167 | 174 |

Borrowers who fully repaid their loans had a slightly longer average and median credit history than borrowers who defaulted.

**Default rate by credit history group:**

| Credit history | Charged-off rate |
|---|---:|
| 0–5 years | 23.10% |
| 5–10 years | 21.26% |
| 10–15 years | 20.18% |
| 15–20 years | 18.77% |
| 20–30 years | 17.79% |
| 30–42 years | 17.99% |
| 42+ years | 23.65% |

**Observations:**
- Default rates generally decrease as credit history increases, up to the 20–30-year group.
- The 30–42-year group has a similar default rate.
- The 42+ years group shows a higher default rate, requiring further investigation of its sample size.

**Preliminary inference:** Credit history length appears to have a relationship with loan repayment outcomes. However, the relationship is not strictly linear. Longer credit history does not necessarily guarantee lower default risk.

The feature will be retained for model development, subject to further evaluation. This analysis identifies an association and does not establish causation.

### Bivariate Analysis – Credit History and Loan Status

Credit history length was compared against loan repayment outcomes using seven credit-history groups.

**Key findings:**

1. Borrowers with 0–5 years of credit history have a charged-off rate of 23.10%.
2. The default rate generally decreases as credit history increases, reaching 17.79% for borrowers with 20–30 years of credit history.
3. The 30–42-year group has a similar default rate of 17.99%.
4. The 42+ years group has a higher default rate of 23.65%, based on 2,025 borrowers (approximately 0.51% of the dataset).

The overall pattern suggests that borrowers with shorter credit histories have higher default rates. However, the relationship is not strictly linear.

**Decision:**
- Retain `credit_history_months` as a potential model feature.
- Exclude the redundant `credit_history_years` feature.
- Do not remove or cap unusually long credit histories based solely on these findings.
- Allow the model to evaluate the predictive contribution of credit history.

**Conclusion:** Credit history length appears to be associated with loan repayment outcomes and may provide useful information for default prediction. This association does not establish causation.

