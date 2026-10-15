---
tags: [ma012, type/concept]
aliases: [df, Degrees of freedom]
lecture: [L01, L02, L04]
---

# Degrees of freedom

> [!abstract] One rule explains every df in this course
> **df = (number of squared terms) − (number of linear constraints / estimated parameters).**

| Situation | Terms | Constraints | df |
|---|---|---|---|
| $\sum(Y_i-\mu)^2/\sigma^2$, $\mu$ known | $n$ | 0 | $n$ |
| $\sum(Y_i-\bar Y)^2/\sigma^2$ | $n$ | $\sum(Y_i-\bar Y)=0$ | $n-1$ |
| one-sample t-test | | | $n-1$ |
| ANOVA $SS_T$ | $n$ | 1 (overall mean) | $n-1$ |
| ANOVA $SS_A$ | $k$ group means | $\sum n_i(\bar Y_{i\cdot}-\bar Y_{\cdot\cdot})=0$ | $k-1$ |
| ANOVA $SS_e$ | $n$ | $k$ (one per group) | $n-k$ |
| two-way interaction | $ab$ cells | rows and columns | $(a-1)(b-1)$ |
| Pearson goodness of fit | $k$ cells | $\sum N_j=n$, plus $m$ estimated parameters | $k-1-m$ |
| Kruskal-Wallis | $a$ group rank sums | fixed total rank sum | $a-1$ |
| Pearson correlation test | $n$ pairs | 2 (intercept and slope of the line) | $n-2$ |
| partial correlation ($m$ variables) | | $m$ | $n-m$ |

**Intuition:** each estimated quantity uses up one dimension of freedom in the data. Fitting parameters bends the model towards the data, so the leftover discrepancy is smaller, and the reference distribution must account for that.

Related: [[Chi-squared distribution]] · [[ANOVA sums of squares and F-test]] · [[Pearson chi-squared GOF test]]
