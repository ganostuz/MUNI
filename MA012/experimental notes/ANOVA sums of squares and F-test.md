---
tags: [ma012, lecture/L02, type/test, exam/1]
aliases: [ANOVA table, F-test, Omnibus F-test, Sum of squares partition, SST SSA SSe]
lecture: L02
exam: 1
---

# ANOVA sums of squares and the omnibus F-test

## 1. Partition of the variability
Split each deviation from the overall mean into a between-group and a within-group part:
$$Y_{ij}-\bar Y_{\cdot\cdot}=(\bar Y_{i\cdot}-\bar Y_{\cdot\cdot})+(Y_{ij}-\bar Y_{i\cdot})$$

Square and sum. The cross term vanishes, because $\sum_j(Y_{ij}-\bar Y_{i\cdot})=0$ in every group. This gives:

> [!important] Theorem: $SS_T=SS_A+SS_e$
> $$\underbrace{\sum_i\sum_j(Y_{ij}-\bar Y_{\cdot\cdot})^2}_{SS_T}=\underbrace{\sum_i n_i(\bar Y_{i\cdot}-\bar Y_{\cdot\cdot})^2}_{SS_A}+\underbrace{\sum_i\sum_j(Y_{ij}-\bar Y_{i\cdot})^2}_{SS_e}$$

| SS | Name | Meaning |
|---|---|---|
| $SS_T$ | total | variability of all observations around the overall mean |
| $SS_A$ | between-groups ("regression") | variability of the group means around the overall mean → **explained by factor $A$** |
| $SS_e$ | within-groups / residual | variability of observations around their own group mean → **unexplained** |

## 2. Distributions (counting constraints → [[Degrees of freedom]])
| SS | Values | Constraints | df | Distribution of $SS/\sigma^2$ |
|---|---|---|---|---|
| $SS_T$ | $n$ | 1 | $d_T=n-1$ | $\chi^2(n-1)$ under $H_0$ |
| $SS_A$ | $k$ group means | 1: $\sum n_i(\bar Y_{i\cdot}-\bar Y_{\cdot\cdot})=0$ | $d_A=k-1$ | $\chi^2(k-1)$ under $H_0$ |
| $SS_e$ | $n$ | $k$ (one per group) | $d_e=n-k$ | $\chi^2(n-k)$ **always** |

The df add up just like the SS: $d_T=d_A+d_e$.

## 3. The F statistic
$$F_A=\frac{MS_A}{MS_e}=\frac{SS_A/(k-1)}{SS_e/(n-k)}=\left(\frac{SS_T}{SS_e}-1\right)\frac{n-k}{k-1}$$

> [!important] Theorem (omnibus F-test)
> Under $H_0$: $F_A\sim F(k-1,\,n-k)$ → [[F distribution]].
> Reject $H_0$ if $F_A\ge F_{1-\alpha}(k-1,n-k)$.

## 4. Questions the slides ask you to answer
- **One- or two-sided?** One-sided (right tail). Group differences can only *increase* $MS_A$ relative to $MS_e$.
- **Why an F distribution?**
  - $SS_A/\sigma^2$ and $SS_e/\sigma^2$ are independent $\chi^2$ variables.
  - Their ratio, each divided by its df, is $F$ by definition.
  - The unknown $\sigma^2$ cancels.
- **$E[F_A]$ under $H_0$:** $\dfrac{d_e}{d_e-2}=\dfrac{n-k}{n-k-2}\approx1$. For the diets: $75/73=1.03$.
- **Limiting values:**
  - Under $H_0$, both $MS_A$ and $MS_e$ estimate $\sigma^2$, so $F_A\to1$.
  - Under $H_1$, $E\,MS_A=\sigma^2+\frac{\sum n_i\alpha_i^2}{k-1}>\sigma^2$, so $F_A\to\infty$ as $n\to\infty$.
- **Unbiased estimator of $\sigma^2$:** $E[MS_e]=\sigma^2$, so $\hat\sigma^2=MS_e$.

## 5. The ANOVA table
| Source | df | SS | MS | F | p |
|---|---|---|---|---|---|
| factor $A$ | $k-1$ | $SS_A$ | $MS_A=SS_A/d_A$ | $F_A=MS_A/MS_e$ | $p$ |
| residuals | $n-k$ | $SS_e$ | $MS_e=SS_e/d_e$ | | |
| total | $n-1$ | $SS_T$ | $MS_T=SS_T/d_T$ | | |

**Diet example:**

| Source | df | SS | MS | F | p |
|---|---|---|---|---|---|
| Diet | 2 | 71.1 | 35.55 | 6.197 | 0.00323 |
| Residuals | 75 | 430.2 | 5.74 | | |
| Total | 77 | 501.3 | | | |

- Critical value: `qf(0.95, 2, 75)` = 3.119.
- $6.197>3.119$ → **reject $H_0$**: at least two diets differ in mean weight loss.

> [!example] Filling in a table (typical exam task)
> Given $k=3$, $n=78$, $SS_A=71.1$, $SS_T=501.3$:
> 1. $SS_e=430.2$, $d_A=2$, $d_e=75$.
> 2. $MS_A=35.55$, $MS_e=5.736$.
> 3. $F=6.20$, compared with $F_{0.95}(2,75)=3.12$.

## 6. Two simulated datasets (slides 14–15)
Both have the same $SS_T=393$:
- **Dataset 1:** $SS_A=353.8$, $F=189.6$ → the groups clearly differ.
- **Dataset 2:** $SS_A=0.7$, $F=0.037$ → no difference.

**Lesson:** what matters is the share of the variability that is *between* groups.

**Link to regression:** $R^2=SS_A/SS_T$ (diets: $71.1/501.3=0.14$), the proportion of variability explained by the factor → [[Linear regression model]].

```r
summary(aov(WeightLoss ~ Diet, data = dat))
anova(lm(WeightLoss ~ Diet, data = dat))   # identical table
```

Related: [[One-way ANOVA]] · [[Model and submodel]] · [[Chi-squared distribution]]
