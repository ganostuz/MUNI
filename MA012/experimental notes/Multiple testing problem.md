---
tags: [ma012, lecture/L01, lecture/L02, type/concept, exam/1]
aliases: [Familywise error rate, FWER, Multiple comparisons problem]
lecture: [L01, L02]
exam: [1, 13]
---

# Multiple testing problem

**Problem:** compare $k$ group means pairwise with t-tests, each at level $\alpha$.
- Number of tests: $s=\binom k2$.
- If the tests were independent, the **familywise error rate** (the probability of at least one false rejection) would be
$$\alpha_{FW}=1-(1-\alpha)^s.$$

| $k$ | $s$ | $\alpha_{FW}$ ($\alpha=0.05$) |
|---|---|---|
| 2 | 1 | 0.05 |
| 3 | 3 | 0.14 |
| 4 | 6 | 0.26 |
| 5 | 10 | 0.40 |

So with 3 diets, pairwise t-tests have a 14 % chance of a false "discovery", not 5 %.

## Correct procedure
1. One **omnibus test** first: [[One-way ANOVA]] F-test, or [[Kruskal-Wallis test]] for the nonparametric case.
2. **Only if it rejects**, run **post-hoc tests** that control $\alpha_{FW}$ → [[Post-hoc multiple comparisons]] (Bonferroni, Tukey HSD, Scheffé).

**Bonferroni idea:** use $\alpha/s$ per test. Then $\alpha_{FW}\le s\cdot\frac\alpha s=\alpha$.

```r
p.adjust(c(0.012, 0.03, 0.2), method = "bonferroni")
pairwise.t.test(dat$WeightLoss, dat$Diet, p.adjust.method = "bonferroni")
```

Related: [[Type I and II errors and power]] · [[Hypothesis testing]]
