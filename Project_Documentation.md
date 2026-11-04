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

5. **Home Ownership:** MORTGAGE is the most common home ownership category, representing approximately 50.08% of borrowers.

6. **Verification Status:** Verified is the most frequent individual category, followed by Source Verified and Not Verified.

7. **Loan Purpose:** Debt consolidation is the most common loan purpose, accounting for approximately 59.21% of records. Credit card repayment is the second most common purpose.

8. **Initial Listing Status:** The F category is more common than W.

9. **Application Type:** Individual applications dominate the dataset, representing approximately 99.82% of records.

### Overall Inference

The dataset primarily consists of individual borrowers with mortgage-based home ownership, relatively long employment histories and 36-month loan terms. Debt consolidation is the predominant reason for borrowing.
