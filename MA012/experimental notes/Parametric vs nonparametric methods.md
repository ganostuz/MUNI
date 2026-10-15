---
tags: [ma012, lecture/L01, lecture/L03, type/concept, exam/2]
aliases: [Nonparametric methods, Parametric methods, Distribution-free]
lecture: [L01, L03]
exam: 2
---
# Parametric vs nonparametric methods

| | **Parametric** | **Nonparametric** |
|---|---|---|
| Assumption | a specific family of distributions, e.g. $N(\mu,\sigma^2)$ | only general properties (continuity, symmetry, at least ordinal data) |
| Works with | parameters (means, variances) | ranks, signs, permutations of the data |
| Examples | [[t-tests]], [[One-way ANOVA]], [[Pearson correlation]], [[Linear regression model]] | [[Sign test]], [[Wilcoxon signed-rank test]], [[Mann-Whitney-Wilcoxon test]], [[Kruskal-Wallis test]], [[Spearman correlation]], [[Kendall tau]], [[Permutation tests]] |
| Data | interval/ratio | also ordinal |
| Power when the assumptions hold | highest | slightly lower |
| Robustness to outliers and skewness | low | high |

**Semiparametric** methods combine both, e.g. the Cox proportional hazards model.

## Why rank tests are distribution-free
- With no ties, every permutation of ranks has probability $1/n!$ under $H_0$, **whatever the underlying continuous distribution** → [[Order statistics and ranks]].
- So the null distribution of a rank statistic can be tabulated once.

## Parametric ↔ nonparametric counterparts
| Question | Parametric | Nonparametric |
|---|---|---|
| location of one sample / paired data | one-sample or paired t-test | [[Sign test]], [[Wilcoxon signed-rank test]] |
| two independent samples | two-sample t-test / Welch | [[Mann-Whitney-Wilcoxon test]] |
| $k\ge3$ independent samples | [[One-way ANOVA]] | [[Kruskal-Wallis test]], [[Median test]], [[Van der Waerden test]] |
| association | [[Pearson correlation]] | [[Spearman correlation]], [[Kendall tau]] |
| any statistic, small $n$ | — | [[Permutation tests]] |

**Choose nonparametric when:**
- the data are ordinal, or
- the samples are small **and** clearly non-normal, or
- there are outliers.

Related: [[00 Choosing a test]]
