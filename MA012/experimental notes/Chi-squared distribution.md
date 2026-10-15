---
tags: [ma012, lecture/L01, type/distribution]
aliases: [chi2, Chi-square distribution, χ2]
lecture: [L01, L02, L03, L04]
---

# Chi-squared distribution $\chi^2(k)$

> [!info] Definition
> If $Z_1,\dots,Z_k$ are i.i.d. $N(0,1)$, then $\sum_{i=1}^k Z_i^2\sim\chi^2(k)$, where $k$ = **degrees of freedom**.

**Properties:**
- $E=k$ and $\mathrm{Var}=2k$.
- Support $[0,\infty)$, right-skewed; it becomes more symmetric as $k$ grows.

## Key results (normal sample $Y_1,\dots,Y_n$)
$$W=\frac1{\sigma^2}\sum_{i=1}^n(Y_i-\mu)^2\sim\chi^2(n),\qquad V=\frac1{\sigma^2}\sum_{i=1}^n(Y_i-\bar Y)^2=\frac{(n-1)S^2}{\sigma^2}\sim\chi^2(n-1)$$

**Why $n-1$?** $\bar Y$ is itself random, and the deviations satisfy one constraint $\sum(Y_i-\bar Y)=0$, so one degree of freedom is lost → [[Degrees of freedom]].

## Where it appears
| Statistic | df | Note |
|---|---|---|
| ANOVA sums of squares $SS/\sigma^2$ | $n-1$, $k-1$, $n-k$ | [[ANOVA sums of squares and F-test]] |
| Pearson goodness-of-fit $K$ | $k-1-m$ | [[Pearson chi-squared GOF test]] |
| Kruskal-Wallis $Q$, median test, Van der Waerden | $a-1$ | [[Kruskal-Wallis test]] |
| Jarque-Bera | 2 | [[Normality tests]] |
| independence in contingency tables | $(r-1)(c-1)$ | [[Contingency tables]] |

**All these tests are right-tailed:** reject if the statistic is $\ge\chi^2_{1-\alpha}(df)$.

| df | 1 | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|---|
| $\chi^2_{0.95}$ | 3.841 | 5.991 | 7.815 | 9.488 | 11.070 | 12.592 |

```r
qchisq(0.95, df = 3)                     # 7.815
pchisq(K, df, lower.tail = FALSE)        # p-value
```

Related: [[F distribution]] (a ratio of two scaled $\chi^2$) · [[Student t distribution]]
