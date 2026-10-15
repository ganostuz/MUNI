---
tags: [ma012, moc]
aliases: [Home, Index, MOC]
---

# MA012 Statistics II — Home

> [!abstract] How this vault is organised
> - **Hubs** (prefixed `00`): this page, the [[00 Exam 2025 topics|exam map]], [[00 Choosing a test]], the [[00 R cheatsheet]] and the [[00 Slide errata]].
> - **Lecture overviews** (prefixed `L0x`): one per lecture, in the order the material was taught, linking to the concept notes.
> - **Concept notes** (no prefix): one note per distribution, test, group of tests or idea. They link to each other, so you can follow the graph view.
> - Notes for exam topics that have **not been lectured yet** exist as stubs (tag `#stub`) holding the exam requirements, ready to be filled in.

> [!tip] How to study with these notes
> 1. Read the lecture overview to see how the pieces fit.
> 2. Work through each concept note: *idea → model/hypotheses → statistic → decision → R*.
> 3. Redo every worked example by hand, then in R. Every example number in these notes was recomputed and matches the slides unless the [[00 Slide errata|errata]] say otherwise.
> 4. Use the "Exam checklist" callout at the bottom of each note for revision.

## Lectures

| # | Lecture | Exam topics |
|---|---|---|
| 1 | [[L01 Introduction]] — data, distributions, hypothesis testing | 13 |
| 2 | [[L02 Analysis of variance]] — one-way and two-way ANOVA, post-hoc tests | 1 |
| 3 | [[L03 Rank tests]] — ranks, sign/Wilcoxon/Mann-Whitney/Kruskal-Wallis, permutation tests | 2 |
| 4 | [[L04 Goodness-of-fit tests]] — Pearson chi-squared, ECDF, Kolmogorov-Smirnov, normality | 3, 4 |
| 5 | [[L05 Correlation analysis]] — Pearson, Spearman, Kendall, partial and multiple correlation | 5 |

## Concept notes by area

**Data and description**
[[Data types]] · [[Tidy data and tidyverse]] · [[Descriptive statistics and EDA]] · [[Q-Q plot]] · [[Skewness and kurtosis]]

**Probability distributions**
[[Random variable and distribution]] · [[Random sample]] · [[Normal distribution]] · [[Chi-squared distribution]] · [[Student t distribution]] · [[F distribution]] · [[Binomial distribution]] · [[Multinomial distribution]] · [[Exponential distribution]] · [[Bivariate normal distribution]] · [[Central limit theorem]] · [[Degrees of freedom]] · [[Probability integral transform]]

**Testing principles**
[[Hypothesis testing]] · [[p-value]] · [[Type I and II errors and power]] · [[Confidence interval]] · [[Multiple testing problem]] · [[Parametric vs nonparametric methods]] · [[Paired vs two-sample design]]

**Parametric tests and models**
[[t-tests]] · [[One-way ANOVA]] · [[ANOVA parametrizations]] · [[ANOVA sums of squares and F-test]] · [[Model and submodel]] · [[ANOVA assumptions and diagnostics]] · [[Levene and Bartlett tests]] · [[Post-hoc multiple comparisons]] · [[Two-way ANOVA]] · [[Types of sums of squares]]

**Rank and permutation tests**
[[Order statistics and ranks]] · [[Sign test]] · [[Wilcoxon signed-rank test]] · [[Mann-Whitney-Wilcoxon test]] · [[Kruskal-Wallis test]] · [[Median test]] · [[Van der Waerden test]] · [[Permutation tests]]

**Goodness of fit**
[[Pearson chi-squared GOF test]] · [[Empirical CDF]] · [[Kolmogorov-Smirnov test]] · [[Normality tests]] · [[Shapiro-Wilk test]] · [[Lilliefors test]]

**Correlation**
[[Pearson correlation]] · [[Fisher z-transformation]] · [[Correlation pitfalls]] · [[Covariance and correlation matrix]] · [[Spearman correlation]] · [[Kendall tau]] · [[Partial correlation]] · [[Multiple correlation]]

**Not yet lectured (stubs)**
[[Linear regression model]] · [[Regression diagnostics]] · [[Multicollinearity and stepwise regression]] · [[Principal component analysis]] · [[Logistic regression]] · [[Contingency tables]] · [[Bootstrap]]
