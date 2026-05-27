# 🔧 dplyr Cheat Sheet

> dplyr is R's answer to pandas. Master these verbs and you can manipulate any dataset.

---

## The 6 Core Verbs

```r
library(dplyr)  # included in tidyverse

filter()     # Keep rows that match a condition
select()     # Keep or drop columns
mutate()     # Create or modify columns
group_by()   # Group rows for aggregation
summarise()  # Aggregate to one row per group
arrange()    # Sort rows
```

---

## filter() — Row Conditions

```r
# Basic comparison
filter(df, age > 30)
filter(df, status == "Active")
filter(df, name != "Unknown")

# Multiple conditions (AND)
filter(df, age > 30, income > 5000)
filter(df, age > 30 & income > 5000)    # same thing

# OR condition
filter(df, age < 25 | age > 65)

# IN list
filter(df, area %in% c("Urban", "Semiurban"))

# NOT IN list
filter(df, !area %in% c("Rural"))

# Between
filter(df, between(age, 25, 45))
filter(df, age >= 25, age <= 45)       # same

# Null handling
filter(df, !is.na(income))             # remove NA
filter(df, is.na(income))              # only NA

# String matching
filter(df, str_detect(name, "^A"))     # starts with A
filter(df, str_detect(name, "son$"))   # ends with son
```

---

## select() — Column Selection

```r
# Keep specific columns
select(df, name, age, income)

# Drop columns
select(df, -id, -created_at)

# Range
select(df, col1:col5)

# By type
select(df, where(is.numeric))
select(df, where(is.character))

# Name patterns
select(df, starts_with("amount"))
select(df, ends_with("_id"))
select(df, contains("income"))

# Rename while selecting
select(df, customer = name, salary = income)

# Reorder (bring cols to front)
select(df, id, everything())
```

---

## mutate() — Create / Modify Columns

```r
# Create new column
mutate(df, dti = loan / income)
mutate(df, total = col1 + col2 + col3)

# Modify existing column
mutate(df, income = income * 1.1)

# Multiple columns at once
mutate(df,
  dti          = loan / income,
  total_income = income + other_income,
  label        = "processed"
)

# Conditional: ifelse
mutate(df, risk = ifelse(dti > 0.5, "High", "Low"))

# Conditional: case_when (multiple conditions)
mutate(df, tier = case_when(
  score >= 750 ~ "Excellent",
  score >= 700 ~ "Good",
  score >= 650 ~ "Fair",
  TRUE         ~ "Poor"        # else
))

# Type conversion
mutate(df,
  age      = as.integer(age),
  category = as.factor(category),
  date     = as.Date(date_string)
)

# String operations
mutate(df,
  name_lower = tolower(name),
  name_clean = trimws(name),
  initials   = str_sub(name, 1, 1)
)

# Math
mutate(df,
  income_log  = log(income),
  income_sqrt = sqrt(income),
  income_std  = scale(income)[, 1]    # z-score
)
```

---

## group_by() + summarise() — Aggregation

```r
# Basic aggregation
df %>%
  group_by(area) %>%
  summarise(avg_income = mean(income, na.rm = TRUE))

# Multiple aggregations
df %>%
  group_by(area) %>%
  summarise(
    count        = n(),
    avg_income   = mean(income,      na.rm = TRUE),
    total_loans  = sum(loan_amount,  na.rm = TRUE),
    median_loan  = median(loan_amount, na.rm = TRUE),
    max_income   = max(income,       na.rm = TRUE),
    approval_rate = mean(is_approved) * 100
  )

# Multiple grouping columns
df %>%
  group_by(area, education) %>%
  summarise(
    count = n(),
    avg   = mean(income, na.rm = TRUE),
    .groups = "drop"                    # ungroup after summarise
  )

# Counting
df %>% count(category)
df %>% count(category, sort = TRUE)    # sorted
df %>% count(col1, col2)               # two columns

# Add percentage
df %>%
  count(status) %>%
  mutate(pct = round(n / sum(n) * 100, 1))

# With filter
df %>%
  filter(!is.na(income)) %>%
  group_by(area) %>%
  summarise(avg = mean(income))
```

---

## arrange() — Sorting

```r
# Ascending (default)
arrange(df, income)

# Descending
arrange(df, desc(income))

# Multiple columns
arrange(df, area, desc(income))

# Top N
df %>% arrange(desc(income)) %>% head(10)
df %>% slice_max(income, n = 10)       # cleaner
df %>% slice_min(income, n = 5)
```

---

## Combining Verbs with Pipe

```r
# Complete analysis pipeline
result <- df %>%
  filter(!is.na(income), loan_amount > 0) %>%       # 1. clean
  mutate(
    dti           = loan_amount / income,             # 2. features
    risk_category = ifelse(dti > 0.5, "High", "Low")
  ) %>%
  group_by(area, risk_category) %>%                  # 3. group
  summarise(
    count         = n(),
    avg_loan      = round(mean(loan_amount), 2),
    approval_rate = round(mean(approved) * 100, 1),
    .groups = "drop"
  ) %>%
  arrange(area, desc(approval_rate))                 # 4. sort

print(result)
```

---

## Helper Functions

```r
# Unique values
n_distinct(df$col)        # number of unique values
unique(df$col)             # vector of unique values

# Ranking
dense_rank(df$income)     # 1,2,2,3 (no gaps)
row_number(df$income)     # 1,2,3,4 (unique)
percent_rank(df$income)   # 0 to 1

# Lead / Lag (time series)
lag(df$value, 1)          # previous value
lead(df$value, 1)         # next value

# Cumulative
cumsum(df$value)
cumprod(df$value)
cummax(df$value)
cummin(df$value)

# Sample
sample_n(df, 100)                    # random 100 rows
sample_frac(df, 0.1)                 # random 10%

# Slice
slice_head(df, n = 5)                # first 5
slice_tail(df, n = 5)                # last 5
slice_sample(df, n = 10)             # random 10
slice_max(df, order_by = col, n = 5) # top 5
```

---

## Reshaping Data

```r
# Wide to Long (pivot_melt in pandas)
df_long <- df %>%
  pivot_longer(
    cols      = c(jan, feb, mar),   # columns to pivot
    names_to  = "month",            # new col with old names
    values_to = "value"             # new col with values
  )

# Long to Wide (pivot_table in pandas)
df_wide <- df_long %>%
  pivot_wider(
    names_from  = "month",
    values_from = "value"
  )
```

---

## Quick Reference

```r
# Most used combination:
df %>%
  filter(condition) %>%           # keep rows
  select(col1, col2, col3) %>%    # keep cols
  mutate(new_col = ...) %>%       # create col
  group_by(group_col) %>%         # group
  summarise(stat = ...) %>%       # aggregate
  arrange(desc(stat))             # sort
```
