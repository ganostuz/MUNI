---
tags: [ma012, lecture/L01, type/concept]
aliases: [Measurement scales, Stevens scales, Nominal data, Ordinal data, Interval data, Ratio data]
lecture: L01
---

# Data types (measurement scales)

Stevens (1946) classifies data by **which operations make sense**. Each level adds operations to the previous one.

| Scale | Allowed operations | Kind | R type | Examples |
|---|---|---|---|---|
| **Nominal** | $=,\neq$, set membership | categorical, discrete | `factor` | blood type, gender, region |
| **Ordinal** | + order: $<,>$, sorting, ranks | categorical, discrete | `ordered` factor | education level, grades, competition ranking |
| **Interval** | + distance: $+,-$ | numeric, usually continuous | `numeric`, `integer` | °C, calendar year |
| **Ratio** | + ratio: $\times,\div$; zero is fixed | numeric, usually continuous | `numeric`, `integer` | mass, length, duration, K |

**Special cases:**
- **Dichotomous data** have 2 categories (often `FALSE`/`TRUE`).
- A **dummy variable** is a 0/1 indicator of membership in one category.

**Discrete vs continuous** (by countability of the possible values):
- **Discrete** values can be listed (finitely or countably many), e.g. number of children, grades.
- **Continuous** values form an interval and are arbitrarily precise, e.g. height, time.
- Continuous data can be **binned into intervals** that act as ordered categories, at a loss of information. That is exactly what the [[Pearson chi-squared GOF test]] does.

## Why it matters: data type → method

| Data | Typical location test | Typical association measure |
|---|---|---|
| nominal | — | [[Contingency tables]] |
| ordinal | rank tests ([[Sign test]], [[Mann-Whitney-Wilcoxon test]], [[Kruskal-Wallis test]]) | [[Spearman correlation]], [[Kendall tau]] |
| interval/ratio, ≈ normal | [[t-tests]], [[One-way ANOVA]] | [[Pearson correlation]] |

> [!warning] Interval vs ratio
> "20 °C is twice as warm as 10 °C" is meaningless, because the zero of the Celsius scale is arbitrary. In kelvin (a ratio scale) such statements are valid.

Related: [[Parametric vs nonparametric methods]] · [[Tidy data and tidyverse]]
