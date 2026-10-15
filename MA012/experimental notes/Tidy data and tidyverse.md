---
tags: [ma012, lecture/L01, type/R]
aliases: [tidyverse, dplyr, ggplot2, Tidy data]
lecture: L01
---

# Tidy data and the tidyverse

## Tidy data (Wickham, 2014)
1. Every **column is a variable**.
2. Every **row is an observation**.
3. Every **cell is a single value**.

For example, ANOVA data in tidy form has one numeric column $Y$ (response) and one `factor` column $A$ (group). See [[One-way ANOVA]].

Tables in R:
- `data.frame` is the classic table.
- `tibble` is its modern, "lazy and surly" version. Tidyverse functions expect tidy tables.

## Core packages
| Package | Purpose |
|---|---|
| `dplyr` | data manipulation verbs |
| `tidyr` | reshaping (`pivot_longer`, `pivot_wider`) |
| `ggplot2` | grammar-of-graphics plots |
| `readr` | fast CSV import |
| `purrr` | functional programming |
| `tibble` | modern data frames |
| `stringr`, `forcats`, `lubridate` | strings, factors, dates |

## dplyr verbs (similar to SQL)
| Task | Verb |
|---|---|
| add/transform columns | `mutate`, `mutate(across(...))`, `transmute`, `rename` |
| select columns | `select`, `pull` |
| select rows | `filter`, `slice`, `slice_head` |
| sort | `arrange` |
| groups | `group_by` (or `.by =` inside the verb) |
| aggregate | `summarise` |
| join | `left_join`, `right_join`, `full_join`, `semi_join`, `anti_join`, `cross_join` |

```r
library(tidyverse)
data <- data |>
  mutate(WeightLoss = WeightPrev - Weight6Weeks,
         Diet   = factor(Diet, labels = c("A", "B", "C")),
         Gender = factor(Gender, labels = c("Female", "Male")))

data |>
  group_by(Diet) |>
  summarise(size = n(), mean = mean(WeightLoss), st.dev = sd(WeightLoss),
            variance = var(WeightLoss), median = median(WeightLoss))
#   Diet size mean st.dev variance median
#   A      24 3.3    2.24   5.02   3.05
#   B      27 3.03   2.52   6.37   3.30
#   C      27 5.15   2.40   5.74   5.4
```

## ggplot2 in four steps
1. Give it tidy data: `ggplot(data, ...)`.
2. Map columns to aesthetics: `aes(x =, y =, colour =, fill =)`.
3. Add geoms with `+`: `geom_point`, `geom_boxplot`, `geom_histogram`, `geom_qq`, ...
4. Adjust: `labs`, `facet_grid`, themes.

```r
data |> ggplot(aes(x = Diet, y = WeightLoss, fill = Diet)) +
  geom_boxplot(outliers = FALSE, alpha = 0.5) +
  geom_jitter(width = 0.2, height = 0) +
  labs(x = "Diet", y = "Weight loss / kg") + guides(fill = "none")
```

Related: [[Descriptive statistics and EDA]] · [[Q-Q plot]] · [[00 R cheatsheet]]
