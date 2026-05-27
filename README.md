# 📊 R Data Analysis Guide
### A practical guide for Business Intelligence & Data Analytics professionals

[![R](https://img.shields.io/badge/R-4.0+-276DC3?style=flat&logo=r&logoColor=white)](https://www.r-project.org/)
[![tidyverse](https://img.shields.io/badge/tidyverse-2.0-1A162D?style=flat)](https://www.tidyverse.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## 🎯 About This Repository

A hands-on reference guide for **BI Analysts and Data Analysts** who want to use R professionally. Built around real workflows: data loading, exploration, cleaning, feature engineering, statistical analysis, and visualization.

Every concept is shown **side by side with Python** so you can leverage what you already know.

**Perfect for:**
- BI/Data Analysts transitioning from Python to R
- Professionals who know Python and need to demonstrate R skills
- Anyone building a data analysis workflow in R from scratch

---

## 📁 Repository Structure

```
r-data-analysis-guide/
│
├── README.md                        ← You are here
├── .gitignore
│
├── 📂 scripts/                      ← Main R scripts (run these)
│   ├── 01_setup_libraries.R         ← Install & load packages
│   ├── 02_data_loading.R            ← Load from CSV, Excel, SQL, URL
│   ├── 03_eda_exploration.R         ← Exploratory Data Analysis
│   ├── 04_data_cleaning.R           ← Handle nulls, outliers, types
│   ├── 05_feature_engineering.R     ← Create new variables
│   ├── 06_statistical_analysis.R    ← Descriptive stats + tests
│   ├── 07_visualization_ggplot2.R   ← Charts and dashboards
│   └── 08_complete_workflow.R       ← End-to-end example
│
├── 📂 docs/                         ← Concept guides (Markdown)
│   ├── 01_r_basics.md               ← Variables, vectors, data frames
│   ├── 02_tidyverse_guide.md        ← dplyr, ggplot2, tidyr
│   ├── 03_eda_guide.md              ← EDA checklist and patterns
│   ├── 04_cleaning_guide.md         ← Data cleaning strategies
│   └── 05_statistics_guide.md       ← Statistical tests reference
│
├── 📂 cheatsheets/                  ← Quick reference cards
│   ├── python_to_r.md               ← Python → R equivalences
│   ├── dplyr_cheatsheet.md          ← dplyr verbs reference
│   ├── ggplot2_cheatsheet.md        ← ggplot2 geoms and themes
│   └── r_base_functions.md          ← Core R functions
│
├── 📂 data/                         ← Sample datasets
│   └── README.md                    ← Dataset descriptions
│
└── 📂 examples/                     ← Complete analysis examples
    └── loan_analysis_complete.R     ← Full example with loan data
```

---

## 🚀 Quick Start

### 1. Install R and RStudio
- **R:** https://cran.r-project.org/
- **RStudio:** https://posit.co/download/rstudio-desktop/

### 2. Install core packages
```r
install.packages(c("tidyverse", "skimr", "corrplot",
                   "scales", "gridExtra", "janitor", "readxl"))
```

### 3. Run your first analysis
```r
# Load libraries
library(tidyverse)
library(skimr)

# Load data
url <- "https://raw.githubusercontent.com/dphi-official/Datasets/master/Loan_Data/loan_train.csv"
df  <- read_csv(url)

# Explore
glimpse(df)
skim(df)

# Analyze
df %>%
  group_by(Loan_Status) %>%
  summarise(
    count       = n(),
    avg_income  = mean(ApplicantIncome),
    avg_loan    = mean(LoanAmount, na.rm = TRUE)
  )
```

---

## 🗺️ Learning Path

### Week 1 — Foundations
- [ ] `scripts/01_setup_libraries.R` — Environment setup
- [ ] `scripts/02_data_loading.R` — Read data from any source
- [ ] `docs/01_r_basics.md` — Core R concepts
- [ ] `cheatsheets/python_to_r.md` — If you know Python

### Week 2 — Exploration & Cleaning
- [ ] `scripts/03_eda_exploration.R` — EDA workflow
- [ ] `scripts/04_data_cleaning.R` — Clean messy data
- [ ] `docs/03_eda_guide.md` — EDA patterns and checklist

### Week 3 — Analysis & Visualization
- [ ] `scripts/05_feature_engineering.R` — Create features
- [ ] `scripts/06_statistical_analysis.R` — Stats and tests
- [ ] `scripts/07_visualization_ggplot2.R` — ggplot2 charts

### Week 4 — Practice
- [ ] `examples/loan_analysis_complete.R` — Full end-to-end
- [ ] `scripts/08_complete_workflow.R` — Your own project

---

## 🔑 The 5 Most Important Concepts in R

### 1. The assignment operator `<-`
```r
# Python: x = 5
# R:      x <- 5
name <- "Fredys"
```

### 2. The pipe operator `%>%`
```r
# Python: df.query("income > 5000").groupby("area").mean()
# R:
df %>%
  filter(income > 5000) %>%
  group_by(area) %>%
  summarise(avg = mean(income))
```

### 3. Data frames and tibbles
```r
# Python: df = pd.DataFrame({'a': [1,2,3], 'b': [4,5,6]})
# R:
df <- tibble(a = c(1, 2, 3), b = c(4, 5, 6))
df$a       # Access column (Python: df['a'])
```

### 4. dplyr verbs (replaces pandas)
```r
filter()    # Python: df[condition]
select()    # Python: df[['col1', 'col2']]
mutate()    # Python: df.assign(new_col = ...)
group_by()  # Python: df.groupby()
summarise() # Python: .agg()
arrange()   # Python: .sort_values()
```

### 5. ggplot2 (replaces matplotlib)
```r
ggplot(df, aes(x = col_x, y = col_y)) +   # data + axes
  geom_histogram() +                        # chart type
  labs(title = "My Chart") +                # labels
  theme_minimal()                            # style
```

---

## 📊 Dataset Used in Examples

**Loan Prediction Dataset** — Binary classification problem
- 614 loan applications
- Target: `Loan_Status` (Y = Approved, N = Rejected)
- Features: income, loan amount, credit history, education, etc.

```r
url <- "https://raw.githubusercontent.com/dphi-official/Datasets/master/Loan_Data/loan_train.csv"
df  <- read_csv(url)
```

---

## 🐍 Coming from Python?

If you know Python/pandas, R will feel familiar in 30 minutes. Key mental model:

| Python (pandas)       | R (tidyverse)             |
|-----------------------|---------------------------|
| `import pandas as pd` | `library(tidyverse)`      |
| `pd.read_csv()`       | `read_csv()`              |
| `df.head()`           | `head(df)`                |
| `df.info()`           | `glimpse(df)`             |
| `df.describe()`       | `summary(df)` / `skim(df)`|
| `df['col']`           | `df$col`                  |
| `df.groupby().agg()`  | `group_by() %>% summarise()`|
| `df[df['x'] > 5]`    | `filter(df, x > 5)`       |
| `df.assign(new=...)`  | `mutate(df, new = ...)`   |

Full reference → `cheatsheets/python_to_r.md`

---

## 👤 Author

**Fredys Caballero**
- Business Intelligence Analyst | 4+ years experience
- Power BI Expert | SQL | Python | R
- LinkedIn: [linkedin.com/in/fredyscaballero](https://linkedin.com/in/fredyscaballero)

---

## 📝 License

MIT License — free to use, modify and share.

---

⭐ **If this guide helped you, consider giving it a star!**
