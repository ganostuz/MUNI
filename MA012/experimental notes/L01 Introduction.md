---
tags: [ma012, lecture/L01, moc]
aliases: [Lecture 1, Introduction]
lecture: L01
date: 2026-09-15
---

# L01 — Introduction

**Story of the lecture:** what statistics does → what kind of data we have → how to handle it in R → the probability tools behind tests → how a test works → why many t-tests are a bad idea (which motivates [[L02 Analysis of variance|ANOVA]]).

## 1. Statistics and its methods
- **Probability theory** builds the mathematical framework for random phenomena.
- **Mathematical statistics** collects, analyses and interprets data with random noise: it fits models, estimates parameters and tests hypotheses, using probability theory.
- Three kinds of method:
  - **Descriptive statistics:** summary numbers and tables → [[Descriptive statistics and EDA]].
  - **Exploratory data analysis (EDA):** plots → [[Descriptive statistics and EDA]], [[Q-Q plot]].
  - **Statistical inference:** from the sample to the population, via estimation, confidence intervals and tests → [[Hypothesis testing]].
- Inference can be **parametric**, **nonparametric** or **semiparametric** → [[Parametric vs nonparametric methods]].

## 2. Data
- Measurement scales: nominal, ordinal, interval, ratio → [[Data types]].
- Tidy data, `dplyr`, `ggplot2` → [[Tidy data and tidyverse]].
- Running example: weight loss after 6 weeks on diets A, B, C (78 people) → used again in [[One-way ANOVA]].

## 3. Probability toolkit
- [[Random variable and distribution]] (PDF, CDF, quantiles) and [[Random sample]] (i.i.d.).
- How the sampling distributions arise from a normal sample:

| Quantity | Distribution | Note |
|---|---|---|
| $Z_i=\frac{Y_i-\mu}{\sigma}$ | $N(0,1)$ | [[Normal distribution]] |
| $W=\frac1{\sigma^2}\sum(Y_i-\mu)^2$ | $\chi^2(n)$ | [[Chi-squared distribution]] |
| $V=\frac1{\sigma^2}\sum(Y_i-\bar Y)^2$ | $\chi^2(n-1)$ | one df lost → [[Degrees of freedom]] |
| $Z=\frac{\bar Y-\mu}{\sigma}\sqrt n$ | $N(0,1)$ | |
| $T=\frac{\bar Y-\mu}{S_Y}\sqrt n=\frac{Z}{\sqrt{V/(n-1)}}$ | $t(n-1)$ | [[Student t distribution]] |

## 4. Hypothesis testing
- $H_0$ and $H_1$, decisions and errors → [[Hypothesis testing]], [[Type I and II errors and power]].
- Three equivalent ways to decide: [[p-value]], critical region, [[Confidence interval]].
- Worked example: Welch two-sample t-test comparing diets A and C → [[t-tests]].
- Normality check per group with Shapiro-Wilk → [[Shapiro-Wilk test]].
- Comparing 3+ groups with pairwise t-tests inflates the error rate → [[Multiple testing problem]] → [[One-way ANOVA]].

> [!check] Exam checklist (topic 13)
> - [ ] State $H_0$/$H_1$ for a research question; one- vs two-sided.
> - [ ] Define the significance level, power, p-value, critical region, CI and how each gives a decision.
> - [ ] Explain why estimating $\mu$ by $\bar Y$ costs one degree of freedom.
