# 📊 Sample Datasets

## Datasets Used in This Guide

---

### Loan Prediction Dataset

**Source:** [dphi-official / Datasets](https://github.com/dphi-official/Datasets)  
**Direct URL:**
```r
url <- "https://raw.githubusercontent.com/dphi-official/Datasets/master/Loan_Data/loan_train.csv"
df  <- read_csv(url)
```

**Description:** Loan approval dataset — binary classification problem.

| Column | Type | Description |
|--------|------|-------------|
| `Loan_ID` | character | Unique identifier |
| `Gender` | character | Male / Female |
| `Married` | character | Yes / No |
| `Dependents` | character | 0 / 1 / 2 / 3+ |
| `Education` | character | Graduate / Not Graduate |
| `Self_Employed` | character | Yes / No |
| `ApplicantIncome` | numeric | Applicant's monthly income |
| `CoapplicantIncome` | numeric | Co-applicant's monthly income |
| `LoanAmount` | numeric | Loan amount requested (thousands) |
| `Loan_Amount_Term` | numeric | Term in months |
| `Credit_History` | numeric | 1 = good history, 0 = bad |
| `Property_Area` | character | Urban / Semiurban / Rural |
| `Loan_Status` | character | **Y = Approved, N = Rejected** ← Target |

**Stats:**
- Rows: 614
- Target balance: ~69% approved, ~31% rejected
- Missing values: LoanAmount (3.3%), Credit_History (8%), Self_Employed (5.2%)

---

## How to Load the Dataset

```r
library(tidyverse)
library(janitor)

# From URL
url <- "https://raw.githubusercontent.com/dphi-official/Datasets/master/Loan_Data/loan_train.csv"
df  <- read_csv(url, show_col_types = FALSE)

# Standardize column names to snake_case
df <- clean_names(df)

# Quick look
glimpse(df)
```

---

## Data Usage

All datasets in this repository are:
- ✅ Free for educational and portfolio use
- ✅ Publicly available with attribution
- ✅ Safe for practice and learning
