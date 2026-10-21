# Credit Risk Analytics & Probability of Default (PD) Modelling

## 1. Project Overview

This is an independent educational project developed using R.

The main objective is to predict whether a credit card customer is likely to default on their payment in the following month.

The project uses Logistic Regression to estimate the Probability of Default (PD), classifies customers into different risk categories, and calculates an illustrative Expected Loss (EL).

- **Dataset:** UCI Default of Credit Card Clients
- **Number of customers:** 30,000
- **Number of variables:** 25
- **Location:** Taiwan
- **Data period:** 2005
- **Source:** [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/350/default+of+credit+card+clients)

## 2. Project Objectives

1. Explore the credit card customer portfolio.
2. Understand customer default behaviour.
3. Build a Logistic Regression model to estimate the Probability of Default (PD).
4. Evaluate model performance using ROC-AUC and other classification metrics.
5. Classify customers into Low, Medium, and High risk categories.
6. Calculate an illustrative Expected Loss using the formula:

   **Expected Loss = PD × LGD × EAD**

   - PD = Probability of Default
   - LGD = Loss Given Default
   - EAD = Exposure at Default

## 3. Project Folder Structure

```
.
├── credit_risk_pd_model.R   # Main R script containing the complete analysis
├── README.md                # Project documentation
├── data/                    # Folder containing the original dataset
└── output/                  # Created automatically; CSV files and PNG charts
```

> The original dataset is not included in this repository.

## 4. Software and Packages Required

**Software**

- R (version 4.x recommended)
- RStudio

**R packages:** `readxl`, `dplyr`, `ggplot2`, `tidyr`, `pROC`, `caret`, `broom`, `scales`

The script automatically installs missing packages when it runs for the first time.

## 5. How to Run the Project

1. **Download the dataset** from the UCI Machine Learning Repository and save the Excel file inside the `data/` folder.
2. **Check the dataset path.** Open `credit_risk_pd_model.R`, find the `DATA_PATH` setting near the top and set it, for example:
   ```r
   DATA_PATH <- "data/credit_card_default.xls"
   ```
   Make sure the filename and extension match your actual file.
3. **Run the script.** Open the project in RStudio, open `credit_risk_pd_model.R` and click **Source**. Alternatively, from a terminal:
   ```bash
   Rscript credit_risk_pd_model.R
   ```
4. **Check the results** in the `output/` folder, which will contain CSV result files and PNG charts.

## 6. Model Settings

| Setting | Default | Meaning |
|---|---|---|
| `TRAIN_FRACTION` | 0.80 | 80% of the data is used for training and 20% for testing |
| `CLASSIFICATION_THRESHOLD` | 0.50 | Customers with predicted PD of 50% or higher are classed as predicted defaulters |
| `ASSUMED_LGD` | 0.45 | Assumes 45% of the exposure is lost when a customer defaults |
| `INCLUDE_DEMOGRAPHICS` | `FALSE` | Excludes SEX, EDUCATION and MARRIAGE from the model |
| `SEED` | 2026 | Random seed for reproducibility |

## 7. Project Methodology

### Step 1: Data Import and Quality Checks

The dataset is imported into R and the metadata row in the workbook is skipped. The script checks for duplicate records, missing values and invalid target values.

### Step 2: Exploratory Data Analysis (EDA)

- Default and non-default customer distributions
- Credit limit and age distributions
- Default rates by age group
- Default rates by recent repayment status

### Step 3: Preparing the Model Data

The customer ID, original target column and demographic variables are excluded from the predictors by default. The model uses 20 numerical predictors:

- Credit limit and age
- Six repayment status variables
- Six bill amount variables
- Six payment amount variables

### Step 4: Splitting the Dataset

- Training data: 80%
- Testing data: 20%

Stratified sampling keeps a similar proportion of defaulters in both datasets.

### Step 5: Building the Model

A Logistic Regression model is fitted with R's `glm()` function using a binomial distribution and logit link. It estimates the Probability of Default for each customer.

### Step 6: Evaluating the Model

ROC-AUC, confusion matrix, accuracy, precision, recall (sensitivity), specificity, Brier score and calibration by decile.

### Step 7: Customer Risk Classification

| Risk band | Predicted PD |
|---|---|
| Low Risk | below 5% |
| Medium Risk | 5% to below 15% |
| High Risk | 15% or higher |

### Step 8: Expected Loss Calculation

**EL = PD × LGD × EAD**

- PD is the model's predicted probability of default.
- LGD is assumed to be 45%.
- Credit limit is used as a proxy for EAD.

This is an illustrative calculation, not a validated estimate of actual credit losses.

## 8. Key Findings

These findings come from the original project run using seed 2026, with 24,000 customers for training and 6,000 for testing.

### Overall Default Rate

The observed default rate was approximately **22.1%**: 6,636 of 30,000 customers defaulted.

### Repayment Behaviour

Recent repayment status was the strongest signal of default risk. Observed default rates rose as repayment delays became more severe:

- Customers paying on time: approximately 13%
- Customers delayed by one month: approximately 34%
- Customers delayed by two months: approximately 69%

### Customer Age

- Youngest age group (25 and below): approximately 26.7%
- Ages 26–35: approximately 19.8% (the lowest of the groups)
- Oldest age group (above 55): approximately 26.5%

### Important Model Predictors

The strongest predictors included, in order of significance:

1. `PAY_0` (most recent repayment status)
2. `PAY_AMT1`
3. `AGE`
4. `BILL_AMT1`
5. `PAY_2`

## 9. Model Performance

Results on the test dataset with a classification threshold of 0.50:

| Metric | Value |
|---|---|
| ROC-AUC | 0.718 |
| Accuracy | 0.804 (80.4%) |
| Precision | 0.705 (70.5%) |
| Recall (Sensitivity) | 0.217 (21.7%) |
| Specificity | 0.974 (97.4%) |

### What do these results mean?

- **ROC-AUC** measures how well the model distinguishes defaulters from non-defaulters.
- **Accuracy** is the proportion of all predictions that were correct.
- **Precision** is the proportion of predicted defaulters who actually defaulted.
- **Recall** is the proportion of actual defaulters the model correctly identified.
- **Specificity** is the proportion of actual non-defaulters the model correctly identified.

Although the model achieved 80.4% accuracy, its recall was only 21.7%, so it missed many customers who actually defaulted. Accuracy alone is therefore not enough to judge the model's usefulness for credit risk management.

## 10. Customer Risk Band Results

| Risk band | Customers | Average predicted PD | Actual default rate |
|---|---|---|---|
| Low Risk | 287 | 3.4% | 7.3% |
| Medium Risk | 1,785 | 10.1% | 14.2% |
| High Risk | 3,928 | 28.8% | 27.3% |

### Interpretation

The actual default rate increased with each risk category, which suggests the model can help rank customers by estimated default risk. However, predicted and observed rates are not identical (the Low Risk band, for example, under-predicts). Model performance may improve by adjusting the classification threshold or applying additional validation methods.

## 11. Data Visualisation and Model Evaluation

The charts below are generated in the `output/` folder when the script runs. They explore credit risk patterns, repayment behaviour and the performance of the PD model.

### 11.1 Default Distribution

The proportion of customers who defaulted and those who did not.

![Default Distribution](output/default_distribution.png)

### 11.2 Default Rate by Age Group

Default rates across customer age groups, showing which groups have relatively higher or lower observed default rates.

[Default Rate by Age Group](output/default_rate_by_age.png)

### 11.3 Default Rate by Repayment Status

How default rates vary with customers' recent repayment status, highlighting repayment delays that may indicate increased credit risk.

[Default Rate by Repayment Status](output/default_rate_by_repayment.png)

### 11.4 ROC Curve

The ROC curve shows how well the Logistic Regression model separates defaulters from non-defaulters across classification thresholds. The model achieved a ROC-AUC of 0.718 in the original test run.

[ROC Curve](output/roc_curve.png)

### 11.5 Model Calibration

The calibration plot compares predicted default probabilities with observed default rates, helping assess whether predicted probabilities are consistent with actual default behaviour.

[Calibration by Decile](output/calibration_by_decile.png)

### 11.6 Customer Risk Segmentation

The observed default rate for each predicted risk band (Low: PD below 5%, Medium: 5% to below 15%, High: 15% or higher).

[Observed Default Rate by Risk Band](output/risk_band_default_rate.png)

## 12. Output Files

When the script runs successfully, the `output/` folder contains:

| File | Description |
|---|---|
| `default_distribution.csv` | Number of default and non-default customers |
| `default_by_age.csv` | Default rates by age group |
| `default_by_repayment_status.csv` | Default rates by recent repayment status |
| `model_performance.csv` | Accuracy, precision, recall, specificity, ROC-AUC and Brier score |
| `risk_band_summary.csv` | Customers summarised by risk category |
| `calibration_by_decile.csv` | Predicted vs observed default rates across deciles |
| `expected_loss_summary.csv` | Illustrative expected loss summaries |
| `logistic_regression_coefficients.csv` | Coefficients, odds ratios and 95% Wald confidence intervals |
| `customer_default_predictions.csv` | Test-set predicted probabilities, predicted classes, risk bands and expected loss estimates |
| `*.png` | Charts: default distribution, credit limit and age distributions, default rate by age and repayment status, ROC curve, calibration, risk bands |

## 13. Project Limitations

- **Illustrative assumptions:** the 45% LGD and the use of credit limit as an EAD proxy have not been validated for real lending decisions.
- **Single train-test split:** there is no cross-validation, out-of-time testing or stability analysis.
- **Simple statistical model:** basic Logistic Regression without feature engineering or extensive tuning. Bill and payment amounts may be highly correlated, so individual coefficients should be interpreted carefully.
- **Old dataset:** the data comes from a Taiwanese credit card issuer in 2005 and may not represent current customers or other countries.
- **Fairness and regulation:** demographic variables are excluded by default. A real credit risk model would require fairness assessment and regulatory review.

## 14. Possible Future Improvements

1. Adjust the classification threshold using cost-sensitive analysis or precision-recall evaluation.
2. Add cross-validation.
3. Compare Logistic Regression with regularised Logistic Regression and tree-based models.
4. Create additional features, such as credit utilisation and payment-to-bill ratios.
5. Calculate Gini and KS statistics.
6. Calculate the Population Stability Index (PSI).
7. Perform out-of-time validation.

## 15. Data Source and Acknowledgements

Dataset: UCI Machine Learning Repository, *Default of Credit Card Clients*.

Original research: Yeh, I. C., & Lien, C. H. (2009). *The Comparisons of Data Mining Techniques for the Predictive Accuracy of Probability of Default of Credit Card Clients.* Expert Systems with Applications, 36(2).

## 16. Disclaimer

This project was developed for educational purposes and as part of a personal analytics portfolio. The model and expected loss estimates are illustrative and should not be used directly for real-world lending, credit approval or financial provisioning decisions.
