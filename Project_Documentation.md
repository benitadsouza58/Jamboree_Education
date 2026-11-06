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

### Bivariate Analysis – Loan Grade vs Loan Status

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

### Bivariate Analysis – Interest Rate vs Loan Status

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
