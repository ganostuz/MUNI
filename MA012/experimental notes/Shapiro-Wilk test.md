---
tags: [ma012, lecture/L01, lecture/L02, lecture/L04, type/test, exam/4]
aliases: [Shapiro Wilk, SW test, shapiro.test]
lecture: [L01, L02, L04]
exam: 4
---

# Shapiro–Wilk test

**Hypotheses:** $H_0$: the data are normal ($\mu,\sigma^2$ unknown); $H_1$: they are not.

$$W=\frac{\left(\sum_{i=1}^na_iX_{(i)}\right)^2}{\sum_{i=1}^n(X_i-\bar X)^2},\qquad 0<W\le1$$

- $X_{(i)}$ are the [[Order statistics and ranks|order statistics]].
- The coefficients $a_i$ come from the expected values and covariances of the order statistics of an i.i.d. $N(0,1)$ sample. R uses Royston's (1982, 1995) algorithm.

**Intuition:**
- The numerator is (up to scale) the best estimate of $\sigma^2$ from the **ordered** data, fitted against normal quantiles.
- The denominator is the ordinary estimate.
- For normal data they agree, so $W\approx1$. $W$ is essentially the squared correlation in the normal [[Q-Q plot]].
- **A small $W$ means reject normality.**

**Properties:**
- among the **most powerful** normality tests;
- R accepts $3\le n\le5000$.

## Uses in the course
- **L01:** weight loss per diet. $p$ = 0.078 (A), 0.961 (B), 0.372 (C) → no group rejects at 5 %.
- **L02:** residuals of the ANOVA model. $W=0.992$, $p=0.916$ → normality OK → [[ANOVA assumptions and diagnostics]].
- **L04:** average restart time. $W=0.888$, $p=1.7\cdot10^{-12}$ → not normal.

```r
shapiro.test(x)
dat |> summarise(.by = Diet, SW_p = shapiro.test(WeightLoss)$p.value)   # per group
shapiro.test(residuals(anova_model))                                     # model residuals
```

Related: [[Normality tests]] · [[Lilliefors test]]
