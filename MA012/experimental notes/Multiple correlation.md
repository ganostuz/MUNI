---
tags: [ma012, lecture/L05, type/test]
aliases: [Multiple correlation coefficient, Coefficient of multiple correlation]
lecture: L05
---

# Multiple correlation

> [!info] Definition
> The **multiple correlation** $\rho_{Z_1\cdot Z_2..Z_m}$ measures the association between $Z_1$ and the **set** $Z_2..Z_m$. It is the largest Pearson correlation between $Z_1$ and any linear combination $Y=\sum_{j\ge2}a_jZ_j$:
> $$r_{Z_1\cdot Z_2..Z_m}=\max_{\mathbf a\ne0}r_{Z_1Y}.$$

## Properties
- $0\le r\le1$: it measures **strength only, not direction**.
- **Biased upwards:** it can only grow when more variables are added, even useless ones. This is the same issue as $R^2$ in regression; in fact $r^2_{Z_1\cdot Z_2..Z_m}=R^2$ of the regression of $Z_1$ on the others → [[Linear regression model]].
- **Answer to the slide question:** an individual Pearson correlation can **never exceed** the multiple correlation, because a single variable is one of the linear combinations in the max.

## Calculation
- $\mathbf R^*$ = the correlation matrix of $Z_2..Z_m$;
- $\mathbf r^*=(r_{Z_1Z_2},\dots,r_{Z_1Z_m})'$.

$$r^2_{Z_1\cdot Z_2..Z_m}=\mathbf r^{*\prime}\,(\mathbf R^*)^{-1}\mathbf r^*$$

**For three variables:**
$$r^2_{Z_1\cdot Z_2Z_3}=\frac{r_{Z_1Z_2}^2+r_{Z_1Z_3}^2-2r_{Z_1Z_2}r_{Z_1Z_3}r_{Z_2Z_3}}{1-r_{Z_2Z_3}^2}$$

## Test
**Hypotheses:** $H_0:\rho=0$; $H_1:\rho>0$.
**Assumption:** an $m$-dimensional normal sample with $n>m$.

$$F=\frac{n-m}{m-1}\cdot\frac{r^2}{1-r^2}\sim F(m-1,\,n-m);\qquad\text{reject if }F\ge F_{1-\alpha}(m-1,n-m).$$

## Examples
**Example 2 (children): IQ vs (Age, Memory, Reading)**
- $r=0.731$, with $n=20$ and $m=4$.
- $F=\frac{16}3\cdot\frac{0.534}{0.466}=6.12>F_{0.95}(3,16)=3.24$, $p=0.006$ → **reject**: IQ is related to the set.

**Example 1: Expense vs (Members, Income)**
- $r=0.988$, with $n=7$ and $m=3$.
- $F=80.7$ on $(2,4)$, $p<0.001$.

```r
R <- cor(Z); i <- 1                        # index of the "target" variable
R_star <- R[-i, -i, drop = FALSE]; r_star <- R[-i, i, drop = FALSE]
r_mult <- sqrt(drop(t(r_star) %*% solve(R_star, r_star)))
Fstat <- (nrow(Z) - ncol(Z)) / (ncol(Z) - 1) * r_mult^2 / (1 - r_mult^2)
pf(Fstat, ncol(Z) - 1, nrow(Z) - ncol(Z), lower.tail = FALSE)
sqrt(summary(lm(Expense ~ Members + Income, data = dat))$r.squared)   # same r
```

Related: [[Partial correlation]] · [[Covariance and correlation matrix]] · [[F distribution]]
