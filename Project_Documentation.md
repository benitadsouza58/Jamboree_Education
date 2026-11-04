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
