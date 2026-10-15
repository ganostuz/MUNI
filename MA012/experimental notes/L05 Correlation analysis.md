---
tags: [ma012, lecture/L05, moc]
aliases: [Lecture 5, Correlation lecture]
lecture: L05
date: 2026-10-13
exam: 5
---

# L05 — Correlation analysis

**Story of the lecture:**
1. Measure association with the Pearson coefficient and learn its limits.
2. Organise many variables in matrices.
3. Use rank versions for ordinal or non-normal data.
4. Separate direct relationships from indirect ones (partial correlation) or from a whole set of variables (multiple correlation).

## Contents
1. Population characteristics vs sample statistics; covariance; Pearson's $r$; t-test; Fisher z → [[Pearson correlation]], [[Fisher z-transformation]].
2. Correlation ≠ independence; Anscombe's quartet; Datasaurus; Simpson's paradox → [[Correlation pitfalls]].
3. [[Bivariate normal distribution]].
4. Variance-covariance and correlation matrices; correlogram → [[Covariance and correlation matrix]].
5. Rank correlations: [[Spearman correlation]]; [[Kendall tau]] (concordant and discordant pairs).
6. [[Partial correlation]] and [[Multiple correlation]].

## Running example 1: 7 households
| Members $M$ | 4 | 2 | 4 | 1 | 5 | 3 | 4 |
|---|---|---|---|---|---|---|---|
| Income $I$ | 100 | 80 | 120 | 30 | 150 | 120 | 130 |
| Expense $E$ | 35 | 30 | 40 | 10 | 60 | 45 | 50 |

```r
dat <- data.frame(Members = c(4, 2, 4, 1, 5, 3, 4),
                  Income  = c(100, 80, 120, 30, 150, 120, 130),
                  Expense = c(35, 30, 40, 10, 60, 45, 50))
```

**Results for Members and Expense** (all recomputed ✔):
| Measure | Value | p |
|---|---|---|
| Pearson | $r=0.890$ | 0.007 |
| Spearman | 0.815 | 0.025 |
| Kendall | $\tau=0.720$ | 0.028 |
| **partial** (Income held fixed) | $-0.30$ | 0.56 |

**Conclusion:** the members–expense link runs through income.

## Running example 2: 20 children
- Variables: Age, Memory, IQ, Reading.
- Age correlates with Reading (0.85) and Memory (0.72).
- Partial correlations reveal IQ–Age $=-0.69$, hidden in the raw correlation ($-0.2$).
- Multiple correlation of IQ with the other three: 0.73.

> [!check] Exam checklist (topic 5)
> - [ ] Pearson, Spearman, Kendall: definition, calculation\*, usage, interpretation of the value, hypotheses of the tests.
> - [ ] Concordant and discordant pairs: definition and counting.
> - [ ] Covariance and correlation matrices: structure, interpretation, correlogram.
