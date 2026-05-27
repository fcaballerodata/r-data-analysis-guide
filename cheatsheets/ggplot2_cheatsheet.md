# 🎨 ggplot2 Cheat Sheet

> ggplot2 is R's visualization library. It works by layering components: data, aesthetics, geometry, labels, and theme.

---

## Core Structure

```r
ggplot(data = df, aes(x = col_x, y = col_y)) +   # 1. Data + axes
  geom_point() +                                   # 2. Chart type
  labs(title = "My Chart", x = "X Axis") +         # 3. Labels
  theme_minimal()                                  # 4. Style
```

**Mental model:**
- `ggplot()` → *what data and what axes*
- `geom_*()` → *how to draw it*
- `aes()` → *which columns map to which visual properties*
- `labs()` → *titles and labels*
- `theme_*()` → *overall look*

---

## Chart Types (geoms)

### Histogram — one numeric variable
```r
ggplot(df, aes(x = income)) +
  geom_histogram(bins = 30, fill = "#3498db", color = "white") +
  labs(title = "Income Distribution", x = "Income", y = "Count") +
  theme_minimal()
```

### Bar Chart — categorical counts
```r
# From raw data (counts automatically):
ggplot(df, aes(x = category)) +
  geom_bar(fill = "#2ecc71") +
  theme_minimal()

# From pre-aggregated data:
df_summary %>%
  ggplot(aes(x = category, y = count)) +
  geom_col(fill = "#2ecc71") +
  theme_minimal()

# Horizontal bar:
ggplot(df, aes(x = reorder(category, n), y = n)) +
  geom_col() +
  coord_flip() +         # flip axes
  theme_minimal()
```

### Boxplot — distribution by group
```r
ggplot(df, aes(x = group, y = value)) +
  geom_boxplot(fill = "#f39c12", alpha = 0.8) +
  theme_minimal()
```

### Scatter Plot — two numeric variables
```r
ggplot(df, aes(x = income, y = loan_amount)) +
  geom_point(alpha = 0.5, color = "#e74c3c", size = 2) +
  geom_smooth(method = "lm", se = TRUE, color = "darkblue") +  # trend line
  theme_minimal()
```

### Line Plot — trends over time
```r
ggplot(df, aes(x = date, y = value)) +
  geom_line(color = "#9b59b6", linewidth = 1.5) +
  geom_point(color = "#9b59b6", size = 2) +
  theme_minimal()
```

### Violin + Boxplot — detailed distribution
```r
ggplot(df, aes(x = group, y = value, fill = group)) +
  geom_violin(alpha = 0.6, trim = FALSE) +
  geom_boxplot(width = 0.15, fill = "white", alpha = 0.9,
               outlier.shape = NA) +
  theme_minimal() +
  theme(legend.position = "none")
```

### Density Plot — smooth distribution
```r
ggplot(df, aes(x = value, fill = group)) +
  geom_density(alpha = 0.5) +
  theme_minimal()
```

---

## Color & Fill by Group

```r
# Color by group (continuous variable):
ggplot(df, aes(x = x, y = y, color = numeric_col)) +
  geom_point() +
  scale_color_gradient(low = "blue", high = "red")

# Color by group (categorical variable):
ggplot(df, aes(x = x, y = y, color = category)) +
  geom_point() +
  scale_color_manual(values = c("#e74c3c", "#27ae60", "#3498db"))

# Fill bars by group:
ggplot(df, aes(x = area, y = count, fill = status)) +
  geom_col(position = "dodge") +   # side by side
  # or: position = "stack"         # stacked
  # or: position = "fill"          # 100% stacked
  theme_minimal()
```

---

## Axes & Scales

```r
# Format as dollars
scale_y_continuous(labels = scales::dollar_format())
scale_x_continuous(labels = scales::dollar_format())

# Format as percentage
scale_y_continuous(labels = scales::percent_format())

# Format with comma (1,000,000)
scale_y_continuous(labels = scales::comma)

# Set axis limits
scale_y_continuous(limits = c(0, 100))
coord_cartesian(ylim = c(0, 100))    # doesn't remove data

# Log scale
scale_x_log10()
scale_y_log10()

# Reverse axis
scale_x_reverse()
```

---

## Labels & Annotations

```r
# Titles and labels
labs(
  title    = "Main Title",
  subtitle = "Subtitle in gray",
  caption  = "Source: Dataset name",
  x        = "X Axis Label",
  y        = "Y Axis Label",
  color    = "Legend Title",
  fill     = "Legend Title"
)

# Add text to points
geom_text(aes(label = label_col), vjust = -0.5, size = 3)
geom_label(aes(label = label_col), fill = "white")

# Add reference line
geom_hline(yintercept = 70, linetype = "dashed", color = "red")
geom_vline(xintercept = 5000, linetype = "dotted", color = "blue")

# Add annotation
annotate("text",  x = 5000, y = 80, label = "Target: 70%",
         color = "red", size = 4, fontface = "italic")
annotate("rect", xmin = 4000, xmax = 6000,
         ymin = -Inf, ymax = Inf, alpha = 0.1, fill = "yellow")
```

---

## Themes

```r
# Clean themes (most common):
theme_minimal()     # minimal grid, no background
theme_classic()     # white background, axis lines only
theme_bw()          # black and white, grid
theme_light()       # light grid
theme_void()        # nothing (for maps)

# Customize theme:
theme(
  # Title
  plot.title       = element_text(face = "bold", size = 14),
  plot.subtitle    = element_text(color = "gray50", size = 11),

  # Axes
  axis.title       = element_text(size = 11),
  axis.text        = element_text(size = 9),
  axis.text.x      = element_text(angle = 45, hjust = 1),

  # Legend
  legend.position  = "top",       # "bottom", "left", "right", "none"
  legend.title     = element_blank(),

  # Grid
  panel.grid.major = element_line(color = "gray90"),
  panel.grid.minor = element_blank(),

  # Background
  plot.background  = element_rect(fill = "white"),
  panel.background = element_rect(fill = "white")
)
```

---

## Multiple Plots

```r
library(gridExtra)

p1 <- ggplot(df, aes(x = col1)) + geom_histogram() + theme_minimal()
p2 <- ggplot(df, aes(x = col2)) + geom_histogram() + theme_minimal()
p3 <- ggplot(df, aes(x = group, y = value)) + geom_boxplot() + theme_minimal()
p4 <- ggplot(df, aes(x = x, y = y)) + geom_point() + theme_minimal()

# 2x2 grid
grid.arrange(p1, p2, p3, p4, ncol = 2)

# Custom layout
grid.arrange(p1, p2, p3, p4,
             nrow = 2, ncol = 2,
             top  = "Dashboard Title")
```

---

## Facets — Small Multiples

```r
# One facet variable:
ggplot(df, aes(x = income)) +
  geom_histogram() +
  facet_wrap(~ area) +        # one panel per area
  theme_minimal()

# Two facet variables:
ggplot(df, aes(x = income)) +
  geom_histogram() +
  facet_grid(education ~ area) +   # rows=education, cols=area
  theme_minimal()
```

---

## Save Plots

```r
# Save last plot
ggsave("my_chart.png", width = 10, height = 6, dpi = 150)

# Save specific plot
ggsave("my_chart.png", plot = p1, width = 10, height = 6, dpi = 150)

# PDF
ggsave("my_chart.pdf", width = 10, height = 6)
```

---

## Complete Example

```r
# Professional bar chart with all elements
df %>%
  group_by(area) %>%
  summarise(
    approval_rate = mean(is_approved) * 100,
    n             = n()
  ) %>%
  ggplot(aes(x = reorder(area, approval_rate),
             y = approval_rate,
             fill = approval_rate)) +
  geom_col(show.legend = FALSE, width = 0.6) +
  geom_text(aes(label = paste0(round(approval_rate, 1), "%")),
            hjust = -0.1, fontface = "bold", size = 4) +
  coord_flip() +
  scale_fill_gradient(low = "#e74c3c", high = "#27ae60") +
  scale_y_continuous(limits   = c(0, 100),
                     labels   = function(x) paste0(x, "%"),
                     expand   = expansion(mult = c(0, 0.1))) +
  geom_hline(yintercept = 70, linetype = "dashed",
             color = "gray40", linewidth = 0.8) +
  annotate("text", x = 0.5, y = 72, label = "Target: 70%",
           hjust = 0, color = "gray40", size = 3.5) +
  labs(
    title    = "Loan Approval Rate by Property Area",
    subtitle = "Dashed line = target (70%)",
    x        = NULL,
    y        = "Approval Rate (%)",
    caption  = "Source: Loan Prediction Dataset"
  ) +
  theme_minimal(base_size = 12) +
  theme(
    plot.title    = element_text(face = "bold", size = 14),
    plot.subtitle = element_text(color = "gray50"),
    panel.grid.major.y = element_blank()
  )
```
