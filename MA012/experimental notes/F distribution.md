---
tags: [ma012, lecture/L02, type/distribution]
aliases: [Fisher-Snedecor distribution, F-distribution]
lecture: [L02, L05]
---

# Fisher–Snedecor F distribution $F(d_1,d_2)$

> [!info] Definition
> If $U\sim\chi^2(d_1)$ and $V\sim\chi^2(d_2)$ are independent, then
> $$F=\frac{U/d_1}{V/d_2}\sim F(d_1,d_2).$$

**Properties:**
- Support $[0,\infty)$, right-skewed.
- $E F=\frac{d_2}{d_2-2}$ for $d_2>2$, which is close to 1.
- $t(k)^2\sim F(1,k)$.

## Where it appears
| Use | df | Note |
|---|---|---|
| ANOVA omnibus test $F_A=MS_A/MS_e$ | $(k-1,\,n-k)$ | [[ANOVA sums of squares and F-test]] |
| two-way ANOVA effects | $(d_{\text{effect}},\,n-ab)$ | [[Two-way ANOVA]] |
| Scheffé's method | uses $F$ quantiles | [[Post-hoc multiple comparisons]] |
| test of multiple correlation | $(m-1,\,n-m)$ | [[Multiple correlation]] |
| Levene's test | $(k-1,\,n-k)$ | [[Levene and Bartlett tests]] |

**The F-test is right-tailed:** reject if $F\ge F_{1-\alpha}(d_1,d_2)$. A large ratio means the "signal" variance exceeds the "noise" variance.

```r
qf(0.95, 2, 75)                        # 3.1186
pf(6.197, 2, 75, lower.tail = FALSE)   # 0.00323
```

Related: [[Chi-squared distribution]] · [[One-way ANOVA]]
