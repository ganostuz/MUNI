---
tags: [ma012, lecture/L01, type/concept]
aliases: [EDA, Exploratory data analysis, Histogram, Boxplot, Descriptive statistics]
lecture: L01
---

# Descriptive statistics and exploratory data analysis

## Sample statistics (numbers)
| Statistic | Formula | R |
|---|---|---|
| sample mean | $\bar X=\frac1n\sum X_i$ | `mean(x)` |
| sample variance | $S_X^2=\frac1{n-1}\sum(X_i-\bar X)^2$ | `var(x)` |
| sample standard deviation | $S_X=\sqrt{S_X^2}$ | `sd(x)` |
| median, quartiles, IQR | 50 %, 25/75 % quantiles; $Q_3-Q_1$ | `median`, `quantile`, `IQR` |
| skewness, kurtosis | see [[Skewness and kurtosis]] | `moments::skewness`, `moments::kurtosis` |
| sample covariance and correlation | see [[Pearson correlation]] | `cov`, `cor` |

The divisor $n-1$ makes $S_X^2$ an **unbiased** estimator of $\sigma^2$. One df is spent on estimating the mean; see [[Degrees of freedom]].

## Plots (what each one shows)
| Plot | Shows | Use it to |
|---|---|---|
| **barplot** | counts of a categorical variable | check group sizes, balance |
| **histogram** | estimate of the density | see shape, skewness, multimodality |
| **boxplot** | median, quartiles, whiskers (1.5·IQR), outliers | compare groups, spot outliers and unequal spread |
| **Q-Q plot** | sample vs theoretical quantiles | check the distribution, especially normality → [[Q-Q plot]] |
| **ECDF plot** | empirical CDF | compare with a model CDF → [[Empirical CDF]] |
| **scatterplot** | two numeric variables | association → [[Pearson correlation]] |

> [!note] The histogram vs the density
> A histogram drawn with `y = after_stat(density)` has total area 1, so it estimates the PDF. A probability is an **area** under the density, not its height. See [[Random variable and distribution]].

Related: [[Tidy data and tidyverse]] · [[Data types]]
