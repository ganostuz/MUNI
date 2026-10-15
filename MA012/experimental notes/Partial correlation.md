---
tags: [ma012, lecture/L05, type/test]
aliases: [Partial correlation coefficient, Precision matrix]
lecture: L05
---

# Partial correlation

> [!info] Definition
> The **partial correlation** $\rho_{Z_1Z_2\cdot Z_3}$ measures the association between $Z_1$ and $Z_2$ **after removing the effect of $Z_3$** (or of all the other variables).

## Formulas
**For three variables:**
$$r_{Z_1Z_2\cdot Z_3}=\frac{r_{Z_1Z_2}-r_{Z_1Z_3}\,r_{Z_2Z_3}}{\sqrt{1-r_{Z_1Z_3}^2}\sqrt{1-r_{Z_2Z_3}^2}}\in[-1,1]$$

**For $m$ variables, all pairs at once:**
1. Invert the correlation matrix: $\mathbf R^{-1}$, the **precision matrix**.
2. Let $\mathbf D$ = the diagonal of $\mathbf R^{-1}$.
3. Then
$$\mathbf R_{\text{part}}=-\mathbf D^{-1/2}\mathbf R^{-1}\mathbf D^{-1/2},\quad\text{with the diagonal set to }1.$$

## Test
**Hypotheses:** $H_0:\rho_{Z_1Z_2\cdot Z_3..Z_m}=0$ vs $H_1:\ne0$.
**Assumption:** an $m$-dimensional normal sample with $n>m$.

$$T=r\sqrt{\frac{n-m}{1-r^2}}\sim t(n-m);\qquad\text{reject if }|T|\ge t_{1-\alpha/2}(n-m).$$

## Example 1 (households)
| Pair | Pearson | Partial (given the third variable) |
|---|---|---|
| Members–Income | 0.92 | 0.58 |
| Members–Expense | 0.89 | **−0.30** |
| Income–Expense | 0.99 | 0.94 |

**Check by hand:** $\dfrac{0.8896-0.9211\cdot0.9866}{\sqrt{(1-0.9211^2)(1-0.9866^2)}}=\dfrac{-0.0192}{0.0635}=-0.30$ ✔.

**Interpretation:**
- Members–Expense is **spurious/mediated**: households with more members have higher income, and income drives expense.
- Income–Expense is a genuine direct relationship.

**Example 2 (children):** IQ–Age is $-0.20$ (Pearson) but $-0.69$ (partial) → a **suppressed** relationship.

```r
R_inv <- solve(cor(Z)); D <- sqrt(diag(R_inv))
R_part <- -R_inv / outer(D, D); diag(R_part) <- 1
d <- nrow(Z) - ncol(Z)
T <- R_part * sqrt(d) / sqrt(1 - R_part^2)
p_part <- 2 * (1 - pt(abs(T), df = d)); diag(p_part) <- 0
ppcor::pcor(Z)                           # same, with p-values
```

## Pearson vs partial: what it tells you
| Pearson | Partial | Insight |
|---|---|---|
| strong | strong | a genuine direct relationship |
| strong | not significant | **spurious or mediated**: driven by a confounder or another variable |
| not significant | strong | **suppressed**: the real relationship is masked by other variables |
| strong | strong, opposite sign | Simpson's paradox / confounding → [[Correlation pitfalls]] |

> [!warning] Many variables, few observations
> With more variables than the data can support (Example 3: 16 economic indicators), $\mathbf R$ is nearly singular and the partial correlations jump to ±1. These values are meaningless; see [[Multicollinearity and stepwise regression]].

Related: [[Multiple correlation]] · [[Covariance and correlation matrix]] · [[Pearson correlation]]
