---
tags: [ma012, lecture/L05, type/test, exam/5]
aliases: [Spearman's rho, Spearman rank correlation, Spearmanův koeficient]
lecture: L05
exam: 5
---

# Spearman's rank correlation

> [!info] Definition
> $R_i$ = rank of $X_i$ among the $X$'s, and $S_i$ = rank of $Y_i$ among the $Y$'s. Then
> $$r^{Sp}_{XY}=r_{RS},$$
> i.e. **the Pearson correlation of the ranks**.
> **Without ties**, this simplifies to
> $$r^{Sp}_{XY}=1-\frac{6\sum_{i=1}^n(R_i-S_i)^2}{n(n^2-1)}.$$

## Meaning and use
- It measures a **monotonic** (not only linear) association.
- $-1\le r^{Sp}\le1$; $\pm1$ means a perfectly monotonic relationship.
- It is the nonparametric analogue of [[Pearson correlation]]: for ordinal data, non-normal data, or data with outliers.

## Test
**Hypotheses:** $H_0$: $X,Y$ are not rank-correlated; $H_1$: they are.

$$T^{Sp}=r^{Sp}\sqrt{\frac{n-2}{1-(r^{Sp})^2}}\approx t(n-2),\qquad U^{Sp}=\sqrt{\frac{n-3}{1.06}}\cdot\frac12\ln\frac{1+r^{Sp}}{1-r^{Sp}}\overset{as.}{\sim}N(0,1)$$

Reject if $|T^{Sp}|\ge t_{1-\alpha/2}(n-2)$ or $|U^{Sp}|\ge u_{1-\alpha/2}$.

## Example 1: Members vs Expense
| $E_i$ | 35 | 30 | 40 | 10 | 60 | 45 | 50 |
|---|---|---|---|---|---|---|---|
| $R_i$ (rank of $E$) | 3 | 2 | 4 | 1 | 7 | 5 | 6 |
| $M_i$ | 4 | 2 | 4 | 1 | 5 | 3 | 4 |
| $S_i$ (rank of $M$) | 5 | 2 | 5 | 1 | 7 | 3 | 5 |

1. $M=4$ occurs three times, at positions 4, 5, 6, so each gets the average rank 5.
2. Because of the ties, compute $r_{RS}$ as a Pearson correlation of the ranks: $r^{Sp}=0.815$.
3. $T^{Sp}=0.815\sqrt{5/(1-0.665)}=3.15$, $p=0.025$ → **reject**.

```r
cor(rank(dat$Members), rank(dat$Expense))                # 0.8154
cor(dat$Members, dat$Expense, method = "spearman")       # same
cor.test(dat$Members, dat$Expense, method = "spearman")  # S = 10.339, p = 0.0254 (warns: ties)
```

> [!tip] R's S
> `cor.test` reports $S=\sum(R_i-S_i)^2$. The coefficient itself is `rho`.

**Spearman vs Pearson (Example 2, children):**
- most values are similar;
- IQ–Age is $-0.20$ (Pearson) vs $0.00$ (Spearman).

Related: [[Kendall tau]] · [[Order statistics and ranks]] · [[Fisher z-transformation]]
