---
tags: [ma012, lecture/L04, moc]
aliases: [Lecture 4, GOF lecture]
lecture: L04
date: 2026-10-06
exam: [3, 4]
---

# L04 — Goodness-of-fit tests

**Question of the lecture:** does a sample come from a specified distribution $F_0$, either fully given or a family with unknown parameters?

**Why we need it:**
- checking assumptions (normality for [[t-tests]] and [[One-way ANOVA]]);
- quality control;
- testing random number generators.

**Hypotheses for every goodness-of-fit (GOF) test:** $H_0$: the data follow $F_0$; $H_1$: they don't.

> [!abstract] The same idea in every test
> 1. Measure the discrepancy between the data and the model.
> 2. Know its distribution when the model is true.
> 3. Reject if it is unusually large.
>
> The tests differ in what they compare:
> - [[Pearson chi-squared GOF test]] compares **counts in bins**.
> - [[Kolmogorov-Smirnov test]] compares **CDFs** (largest gap).
> - CvM and Anderson–Darling compare CDFs by the average squared gap.
> - [[Shapiro-Wilk test]] compares **order statistics**.
> - D'Agostino and Jarque–Bera compare **shape** (skewness and kurtosis).

## Contents
1. [[Pearson chi-squared GOF test]]: observed vs expected counts, [[Multinomial distribution]], $df=k-1-m$, approximation conditions.
   - Examples: boys in families vs $\mathrm{Bin}(5,0.5)$; waiting times vs the [[Exponential distribution]].
2. [[Empirical CDF]] and the [[Kolmogorov-Smirnov test]] (one- and two-sample).
   - Example: restart times vs $\mathrm{Ex}(0.04)$.
3. Normality: [[Normality tests]], [[Skewness and kurtosis]], [[Shapiro-Wilk test]], [[Lilliefors test]], the Cullen–Frey plot, [[Q-Q plot]].
4. Final exercise: [[Probability integral transform]].

## Which test when
| Situation | Test |
|---|---|
| discrete data, or continuous data in bins | [[Pearson chi-squared GOF test]] |
| continuous data, $F_0$ fully specified | [[Kolmogorov-Smirnov test]] |
| two samples, same distribution? | two-sample [[Kolmogorov-Smirnov test]] |
| normality, $\mu,\sigma$ unknown | [[Shapiro-Wilk test]] (most powerful), Anderson–Darling, [[Lilliefors test]] |

> [!check] Exam checklist (topics 3 and 4)
> - [ ] Pearson's test: hypotheses; observed and expected frequencies for discrete and continuous distributions; statistic\*; distribution with given/unknown parameters; decision.
> - [ ] ECDF: definition and interpretation.
> - [ ] K-S test: hypotheses, geometric meaning of $D$, usage.
> - [ ] Names of the normality tests.
