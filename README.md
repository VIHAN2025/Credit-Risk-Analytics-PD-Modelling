Credit Risk Analytics & Probability of Default (PD) Modelling

An independent educational project in R that predicts next-month credit card default using logistic regression, groups customers into PD risk bands, and estimates an illustrative expected loss.

Dataset: UCI Default of Credit Card Clients (30,000 customers, 25 variables, Taiwan, 2005)

Project objectives
Explore the credit card portfolio and understand default behaviour
Build a logistic regression model to estimate each customer's probability of default (PD)
Evaluate discrimination (ROC-AUC) and classification performance on held-out data
Segment customers into low, medium and high risk bands
Produce an illustrative expected loss estimate (EL = PD × LGD × EAD)
Repository structure
.
├── credit_risk_pd_model.R     # Full analysis script
├── README.md
├── data/                      # Place the UCI Excel file here (not included)
└── output/                    # Created automatically when the script runs
    ├── *.csv                  # Result tables
    └── *.png                  # Plots
Requirements
R (4.x recommended) and RStudio
Packages: readxl, dplyr, ggplot2, tidyr, pROC, caret, broom, scales

The script installs any missing packages automatically on first run.

How to run
Download the dataset from the UCI link above and save it as an Excel file.
Open credit_risk_pd_model.R and set DATA_PATH in the settings block at the top, for example:
r
   DATA_PATH <- "data/credit_card_default.xls"
Run the script in RStudio (Source), or from the terminal:
bash
   Rscript credit_risk_pd_model.R
Results are written to the output/ folder.
Settings
Setting	Default	Meaning
TRAIN_FRACTION	0.80	Share of data used for training (stratified split)
CLASSIFICATION_THRESHOLD	0.50	PD cut-off for predicting default
ASSUMED_LGD	0.45	Illustrative loss given default
INCLUDE_DEMOGRAPHICS	FALSE	Add SEX, EDUCATION, MARRIAGE as factors
SEED	2026	Random seed for reproducibility
Methodology
Import and quality checks: the workbook has a metadata row, so it is read with skip = 1. The script checks for duplicates, missing values and invalid target values.
Exploratory analysis: default distribution, credit limit and age distributions, default rate by age group and by recent repayment status (PAY_0).
Model data: the ID, target source column and (by default) the demographic fields are removed. The remaining 20 numeric predictors are credit limit, age, six repayment statuses, six bill amounts and six payment amounts.
Train/test split: 80/20 stratified on the default flag using caret::createDataPartition.
Model: logistic regression (glm, binomial/logit).
Evaluation: ROC-AUC, confusion matrix, accuracy, precision, recall, specificity, Brier score and decile calibration.
Risk bands: Low (PD < 5%), Medium (5% to 15%), High (≥ 15%).
Expected loss: PD × assumed LGD × credit limit, where the credit limit is used as a proxy for exposure at default.
Key findings

From the original run (seed 2026, 24,000 training and 6,000 test customers):

Observed default rate: 22.1% (6,636 of 30,000 customers)
Recent repayment status is the strongest signal. Default rates rise from about 13% for customers paying on time to about 34% at one month delayed and about 69% at two months delayed.
Age: default rates are highest for the youngest (26.7%) and oldest (26.5%) groups and lowest for ages 26 to 35 (19.8%).
Strongest model predictors: PAY_0 (odds ratio about 1.79 per delay level), followed by PAY_AMT1, AGE, BILL_AMT1 and PAY_2.
Metric (test set, threshold 0.50)	Value
ROC-AUC	0.718
Accuracy	0.804
Precision	0.705
Recall (sensitivity)	0.217
Specificity	0.974
Risk band	Customers	Average PD	Actual default rate
Low	287	3.4%	7.3%
Medium	1,785	10.1%	14.2%
High	3,928	28.8%	27.3%

The model ranks risk sensibly (actual default rates rise with each band), but at a 0.50 cut-off it misses most defaulters (recall about 22%). Lowering the threshold trades precision for recall and may suit a lender whose priority is catching defaults.

Output files
File	Description
default_distribution.csv	Default vs non-default counts
default_by_age.csv	Default rate by age group
default_by_repayment_status.csv	Default rate by PAY_0
model_performance.csv	Accuracy, precision, recall, specificity, AUC, Brier score
risk_band_summary.csv	PD risk band summary
calibration_by_decile.csv	Predicted vs observed default by decile
expected_loss_summary.csv	Illustrative expected loss totals
logistic_regression_coefficients.csv	Coefficients, odds ratios, 95% Wald confidence intervals
customer_default_predictions.csv	Test-set PD, predicted class, risk band, expected loss
*.png	Plots, including ROC curve and calibration chart
Limitations
Illustrative assumptions: the 45% LGD is an assumption, and the credit limit is only a proxy for exposure at default. The expected loss figure is not a validated provision.
Single random split: there is no cross-validation, out-of-time test or stability analysis.
Simple model: a basic logistic regression with no feature engineering or tuning. The bill and payment amounts are highly correlated, so individual coefficients should be read with care.
Old, regional data: the data comes from one Taiwanese issuer in 2005 and may not reflect other markets or today's behaviour.
Fairness: demographic fields are excluded by default. A real credit model would need fairness and regulatory review.

This project is for learning and portfolio purposes only and should not be used for real lending decisions.

Possible extensions
Tune the classification threshold using a cost-sensitive or precision-recall view
Add cross-validation and compare with regularised logistic regression or tree-based models
Engineer features such as utilisation and payment-to-bill ratios
Add Gini/KS statistics, Population Stability Index and an out-of-time check
License and data credit

Code released for educational use. Data © UCI Machine Learning Repository: Yeh, I. C., & Lien, C. H. (2009), The comparisons of data mining techniques for the predictive accuracy of probability of default of credit card clients, Expert Systems with Applications, 36(2).
