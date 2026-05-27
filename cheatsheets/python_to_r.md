# 🐍➡️ 🔵 Python to R — Complete Equivalence Guide

> If you know Python and pandas, you already know 80% of R.
> This guide maps every common operation from Python to R syntax.

---

## 1. Setup & Installation

| Python | R |
|--------|---|
| `pip install pandas` | `install.packages("tidyverse")` |
| `import pandas as pd` | `library(tidyverse)` |
| `import numpy as np` | *(included in R base)* |
| `import matplotlib.pyplot as plt` | `library(ggplot2)` *(inside tidyverse)* |
| `import seaborn as sns` | `library(corrplot)` |
| `from scipy import stats` | *(stats included in R base)* |

```r
# Install once, then load every session
install.packages(c("tidyverse", "skimr", "corrplot", "scales", "janitor"))

# Load every session
library(tidyverse)
library(skimr)
library(corrplot)
```

---

## 2. Variables & Data Types

| Python | R |
|--------|---|
| `x = 5` | `x <- 5` |
| `name = "hello"` | `name <- "hello"` |
| `flag = True` | `flag <- TRUE` |
| `nums = [1, 2, 3]` | `nums <- c(1, 2, 3)` |
| `int / float` | `numeric` |
| `str` | `character` |
| `bool` | `logical` |
| `category` | `factor` |
| `datetime` | `Date` / `POSIXct` |

```r
# Python: x = 5
x <- 5

# Python: lista = [10, 20, 30]
vector <- c(10, 20, 30)

# Python: tipo = type(x)
class(x)         # "numeric"
typeof(x)        # "double"
is.numeric(x)    # TRUE
```

---

## 3. Data Frames

### Creating

```r
# Python:
# df = pd.DataFrame({'col1': [1,2,3], 'col2': ['a','b','c']})

# R (data.frame - classic):
df <- data.frame(col1 = c(1, 2, 3), col2 = c("a", "b", "c"))

# R (tibble - modern, recommended):
df <- tibble(col1 = c(1, 2, 3), col2 = c("a", "b", "c"))
```

### Accessing columns

```r
# Python: df['col1']
df$col1          # Dollar sign

# Python: df[['col1', 'col2']]
df %>% select(col1, col2)
```

### Dimensions

```r
# Python: df.shape
dim(df)          # c(rows, cols)
nrow(df)         # rows only
ncol(df)         # cols only

# Python: len(df)
nrow(df)

# Python: df.columns
names(df)
colnames(df)
```

---

## 4. Loading Data

```r
# ── From CSV ──────────────────────────────────────────────
# Python: df = pd.read_csv("file.csv")
df <- read_csv("file.csv")          # tidyverse (recommended)
df <- read.csv("file.csv")          # R base

# With options:
# Python: pd.read_csv("file.csv", sep=";", encoding="latin-1")
df <- read_csv("file.csv", locale = locale(encoding = "latin1"),
               delim = ";")

# ── From URL ──────────────────────────────────────────────
# Python: df = pd.read_csv(url)
df <- read_csv(url)                 # Same syntax!

# ── From Excel ────────────────────────────────────────────
# Python: df = pd.read_excel("file.xlsx")
library(readxl)
df <- read_excel("file.xlsx")
df <- read_excel("file.xlsx", sheet = "Sheet2")

# ── From SQL ──────────────────────────────────────────────
# Python: df = pd.read_sql(query, conn)
library(DBI)
con <- dbConnect(RMySQL::MySQL(),
                 host = "localhost", dbname = "mydb",
                 user = "user",     password = "pass")
df  <- dbGetQuery(con, "SELECT * FROM table WHERE date > '2024-01-01'")
dbDisconnect(con)

# ── From Google Sheets ────────────────────────────────────
# Python: df = pd.read_csv(sheets_url)
url_csv <- "https://docs.google.com/spreadsheets/d/ID/export?format=csv"
df <- read_csv(url_csv)
```

---

## 5. Exploration

```r
# Python: df.head()
head(df)
head(df, 10)

# Python: df.tail()
tail(df)

# Python: df.info()
str(df)       # Structure (types + sample values)
glimpse(df)   # tidyverse version (more readable)

# Python: df.describe()
summary(df)           # Base R
skim(df)              # skimr package (much better!)

# Python: df.shape
dim(df)

# Python: df.columns
names(df)

# Python: df.dtypes
sapply(df, class)

# Python: df.nunique()
sapply(df, n_distinct)

# Python: df['col'].value_counts()
table(df$col)
df %>% count(col)
df %>% count(col) %>% mutate(pct = n / sum(n) * 100)

# Python: df['col'].unique()
unique(df$col)
```

---

## 6. Filtering & Selecting

```r
# ── Filter rows ───────────────────────────────────────────
# Python: df[df['income'] > 5000]
df %>% filter(income > 5000)

# Python: df[df['status'] == 'Active']
df %>% filter(status == "Active")

# Python: df[(df['income'] > 5000) & (df['age'] < 40)]
df %>% filter(income > 5000, age < 40)          # comma = AND

# Python: df[(df['income'] > 5000) | (df['score'] > 700)]
df %>% filter(income > 5000 | score > 700)

# Python: df[df['status'].isin(['A', 'B', 'C'])]
df %>% filter(status %in% c("A", "B", "C"))

# Python: df[~df['status'].isin(['X', 'Y'])]
df %>% filter(!status %in% c("X", "Y"))

# Python: df[df['col'].notna()]
df %>% filter(!is.na(col))
df %>% drop_na(col)

# ── Select columns ────────────────────────────────────────
# Python: df[['col1', 'col2', 'col3']]
df %>% select(col1, col2, col3)

# Python: df.drop(columns=['col1', 'col2'])
df %>% select(-col1, -col2)

# Python: df.select_dtypes(include=['number'])
df %>% select(where(is.numeric))

# Python: df.select_dtypes(include=['object'])
df %>% select(where(is.character))
```

---

## 7. Creating & Modifying Columns

```r
# ── Create new column ─────────────────────────────────────
# Python: df['new'] = df['col1'] + df['col2']
df <- df %>% mutate(new = col1 + col2)

# Python: df['ratio'] = df['loan'] / df['income']
df <- df %>% mutate(ratio = loan / income)

# ── Conditional column (if/else) ──────────────────────────
# Python: df['label'] = np.where(df['score'] > 700, 'Good', 'Bad')
df <- df %>% mutate(label = ifelse(score > 700, "Good", "Bad"))

# ── Multiple conditions (case_when) ───────────────────────
# Python: pd.cut(df['score'], bins=[0,500,700,850], labels=['Low','Mid','High'])
df <- df %>%
  mutate(tier = case_when(
    score < 500 ~ "Low",
    score < 700 ~ "Medium",
    TRUE        ~ "High"      # TRUE = else
  ))

# ── Binary encoding ───────────────────────────────────────
# Python: (df['col'] == 'Yes').astype(int)
df <- df %>% mutate(is_yes = as.integer(col == "Yes"))

# ── Type conversion ───────────────────────────────────────
# Python: df['col'].astype(int)
df <- df %>% mutate(col = as.integer(col))

# Python: df['col'].astype('category')
df <- df %>% mutate(col = as.factor(col))

# Python: pd.to_datetime(df['date'])
df <- df %>% mutate(date = as.Date(date))

# ── String operations ─────────────────────────────────────
# Python: df['col'].str.lower()
df <- df %>% mutate(col = tolower(col))

# Python: df['col'].str.strip()
df <- df %>% mutate(col = trimws(col))

# Python: df['col'].str.replace('old', 'new')
df <- df %>% mutate(col = str_replace(col, "old", "new"))
```

---

## 8. Missing Values

```r
# ── Detect ────────────────────────────────────────────────
# Python: df.isnull().sum()
colSums(is.na(df))

# Python: df.isnull().sum() / len(df) * 100
colMeans(is.na(df)) * 100

# Python: df['col'].isnull().any()
any(is.na(df$col))

# ── Fill / Replace ────────────────────────────────────────
# Python: df['col'].fillna(df['col'].median())
df <- df %>%
  mutate(col = ifelse(is.na(col), median(col, na.rm = TRUE), col))

# Python: df['col'].fillna(df['col'].mode()[0])
mode_val <- names(sort(table(df$col), decreasing = TRUE))[1]
df <- df %>%
  mutate(col = ifelse(is.na(col), mode_val, col))

# Python: df['col'].fillna('Unknown')
df <- df %>%
  mutate(col = ifelse(is.na(col), "Unknown", col))

# ── Drop ──────────────────────────────────────────────────
# Python: df.dropna()
df <- na.omit(df)
df <- df %>% drop_na()

# Python: df.dropna(subset=['col1', 'col2'])
df <- df %>% drop_na(col1, col2)
```

---

## 9. Sorting & Ranking

```r
# Python: df.sort_values('col')
df %>% arrange(col)

# Python: df.sort_values('col', ascending=False)
df %>% arrange(desc(col))

# Python: df.sort_values(['col1', 'col2'], ascending=[True, False])
df %>% arrange(col1, desc(col2))

# Python: df.nlargest(10, 'col')
df %>% slice_max(col, n = 10)

# Python: df.nsmallest(10, 'col')
df %>% slice_min(col, n = 10)
```

---

## 10. GroupBy & Aggregations

```r
# Python: df.groupby('col').mean()
df %>% group_by(col) %>% summarise(mean_val = mean(value))

# Python: df.groupby('col').agg({'val': 'mean', 'count': 'sum'})
df %>%
  group_by(col) %>%
  summarise(
    mean_val = mean(value,  na.rm = TRUE),
    total    = sum(count,   na.rm = TRUE),
    n        = n()                           # row count
  )

# Python: df.groupby(['col1', 'col2']).size().reset_index()
df %>% count(col1, col2)

# Python: df.groupby('col')['val'].transform('mean')
df %>% mutate(group_mean = mean(value), .by = col)
# or:
df %>% group_by(col) %>% mutate(group_mean = mean(value))

# Python: pd.crosstab(df['col1'], df['col2'])
table(df$col1, df$col2)

# Python: pd.crosstab(df['col1'], df['col2'], normalize='index')
prop.table(table(df$col1, df$col2), margin = 1) * 100
```

---

## 11. Merging & Joining

```r
# Python: pd.merge(df1, df2, on='key', how='left')
left_join(df1, df2, by = "key")

# Python: pd.merge(df1, df2, on='key', how='inner')
inner_join(df1, df2, by = "key")

# Python: pd.merge(df1, df2, on='key', how='outer')
full_join(df1, df2, by = "key")

# Python: pd.merge(df1, df2, on='key', how='right')
right_join(df1, df2, by = "key")

# Python: pd.concat([df1, df2])
bind_rows(df1, df2)

# Python: pd.concat([df1, df2], axis=1)
bind_cols(df1, df2)
```

---

## 12. Statistics

```r
# Python: np.mean(x)          R: mean(x)
# Python: np.median(x)        R: median(x)
# Python: np.std(x)           R: sd(x)
# Python: np.var(x)           R: var(x)
# Python: np.sum(x)           R: sum(x)
# Python: np.min(x) / max(x)  R: min(x) / max(x)
# Python: np.quantile(x, 0.25) R: quantile(x, 0.25)

# Correlations:
# Python: df.corr()
cor(df_numeric)

# Python: scipy.stats.pearsonr(x, y)
cor.test(x, y, method = "pearson")

# Python: scipy.stats.ttest_ind(a, b)
t.test(a, b)
t.test(value ~ group, data = df)       # Formula interface

# Python: scipy.stats.chi2_contingency(table)
chisq.test(table(df$col1, df$col2))

# Python: scipy.stats.zscore(x)
scale(x)
```

---

## 13. Visualization (matplotlib → ggplot2)

```r
# Basic structure:
# ggplot(data, aes(x = x_col, y = y_col)) + geom_*() + labs() + theme_*()

# ── Histogram ─────────────────────────────────────────────
# Python: plt.hist(df['col'], bins=30)
ggplot(df, aes(x = col)) +
  geom_histogram(bins = 30) +
  theme_minimal()

# ── Bar chart ─────────────────────────────────────────────
# Python: df['col'].value_counts().plot(kind='bar')
df %>%
  count(col) %>%
  ggplot(aes(x = col, y = n)) +
  geom_col() +
  theme_minimal()

# ── Boxplot ───────────────────────────────────────────────
# Python: df.boxplot(column='val', by='group')
ggplot(df, aes(x = group, y = val)) +
  geom_boxplot() +
  theme_minimal()

# ── Scatter plot ──────────────────────────────────────────
# Python: plt.scatter(df['x'], df['y'])
ggplot(df, aes(x = x_col, y = y_col)) +
  geom_point(alpha = 0.6) +
  theme_minimal()

# ── Line plot ─────────────────────────────────────────────
# Python: plt.plot(df['date'], df['value'])
ggplot(df, aes(x = date, y = value)) +
  geom_line() +
  theme_minimal()

# ── Color by group ────────────────────────────────────────
# Python: sns.scatterplot(x='x', y='y', hue='group')
ggplot(df, aes(x = x_col, y = y_col, color = group)) +
  geom_point() +
  theme_minimal()

# ── Correlation heatmap ───────────────────────────────────
# Python: sns.heatmap(df.corr(), annot=True)
library(corrplot)
corrplot(cor(df_numeric), method = "color", addCoef.col = "black")

# ── Multiple plots ────────────────────────────────────────
# Python: fig, axes = plt.subplots(1, 2)
library(gridExtra)
p1 <- ggplot(df, aes(x = col1)) + geom_histogram()
p2 <- ggplot(df, aes(x = col2)) + geom_histogram()
grid.arrange(p1, p2, ncol = 2)
```

---

## 14. Exporting

```r
# Python: df.to_csv('output.csv', index=False)
write_csv(df, "output.csv")

# Python: df.to_excel('output.xlsx', index=False)
library(writexl)
write_xlsx(df, "output.xlsx")

# Save R object (no Python equivalent):
saveRDS(df, "df.rds")
df <- readRDS("df.rds")

# Save plot:
# Python: plt.savefig('plot.png', dpi=150)
ggsave("plot.png", plot = p, width = 10, height = 6, dpi = 150)
```

---

## 15. The Pipe Operator `%>%`

The single most important concept in modern R.

```r
# Python equivalent: method chaining
# df.query("income > 5000").groupby("area")["loan"].mean()

# R with pipe:
df %>%
  filter(income > 5000) %>%
  group_by(area) %>%
  summarise(avg_loan = mean(loan, na.rm = TRUE))

# Read as: "take df, THEN filter, THEN group by, THEN summarise"
# The %>% passes the result of the left side as the first argument of the right side
```

> **Note:** R 4.1+ introduced a native pipe `|>` that works similarly.
> Both `%>%` (magrittr/tidyverse) and `|>` (native) are widely used.

---

## Quick Reference Card

```r
# Load everything you need:
library(tidyverse)     # dplyr + ggplot2 + readr + tidyr
library(skimr)         # better summary()
library(janitor)       # clean column names
library(corrplot)      # correlation heatmaps

# The 6 dplyr verbs you use 90% of the time:
filter()     # rows
select()     # columns
mutate()     # new columns
group_by()   # grouping
summarise()  # aggregation
arrange()    # sorting

# The 5 key stats functions:
mean(x, na.rm = TRUE)
median(x, na.rm = TRUE)
sd(x, na.rm = TRUE)
cor(df_num)
table(df$col)
```
