---
tags: [ma012, moc, exam/13]
aliases: [Test chooser, Which test, Decision guide]
exam: 13
---

# Choosing the right method (exam topic 13)

> [!tip] Three questions settle almost every case
> 1. **What is the goal?** Compare locations, check a distribution, or measure association.
> 2. **What is the data type?** Nominal, ordinal or numeric → [[Data types]].
> 3. **What is the design?** One sample, paired, two independent samples, or $k\ge3$ independent groups → [[Paired vs two-sample design]].
>
> Then ask: **is normality plausible?** If yes → parametric; if no (small skewed samples, outliers, ordinal data) → nonparametric.

## Comparing locations
| Design | Normal data | Not normal / ordinal |
|---|---|---|
| one sample vs value $x_0$ | one-sample [[t-tests\|t-test]] | [[Wilcoxon signed-rank test]] (symmetric), [[Sign test]] (any shape) |
| paired samples | paired t-test on the differences | Wilcoxon signed-rank or sign test on the differences |
| 2 independent samples | two-sample t-test; Welch if the variances differ | [[Mann-Whitney-Wilcoxon test]] |
| $k\ge3$ independent groups, 1 factor | [[One-way ANOVA]] (Welch ANOVA if the variances differ) → [[Post-hoc multiple comparisons]] | [[Kruskal-Wallis test]] (or [[Median test]], [[Van der Waerden test]]) → pairwise MWW + Bonferroni |
| 2 factors | [[Two-way ANOVA]] | — |
| any statistic, small $n$ | — | [[Permutation tests]] |

## Checking assumptions and distributions
| Question | Method |
|---|---|
| are the data normal? | [[Shapiro-Wilk test]] (default), Anderson–Darling, [[Lilliefors test]] + [[Q-Q plot]] → [[Normality tests]] |
| equal variances across groups? | [[Levene and Bartlett tests]] |
| discrete or binned data from a given distribution? | [[Pearson chi-squared GOF test]] |
| continuous data from a **fully specified** $F_0$? | [[Kolmogorov-Smirnov test]] |
| two samples from the same distribution? | two-sample K-S |
| which family could fit? | Cullen–Frey plot → [[Skewness and kurtosis]] |

## Association
| Data | Method |
|---|---|
| two numeric, ≈ bivariate normal, linear | [[Pearson correlation]] (test with $t(n-2)$; CI via [[Fisher z-transformation]]) |
| ordinal, or monotonic but non-linear, or outliers | [[Spearman correlation]] |
| ordinal; comparing two rankings; many ties | [[Kendall tau]] |
| association of two variables, controlling for others | [[Partial correlation]] |
| one variable vs a set of variables | [[Multiple correlation]] |
| two nominal variables | χ² test of independence → [[Contingency tables]] |
| many numeric variables at once | [[Covariance and correlation matrix]], correlogram, [[Principal component analysis]] |

## Typical exam scenarios
1. **"Yields of 3 wheat varieties, 5 small skewed samples with an outlier."** → [[Kruskal-Wallis test]], then pairwise MWW with Bonferroni.
2. **"Blood pressure before and after a drug, 40 patients, differences look normal."** → paired t-test. If the differences are skewed → [[Wilcoxon signed-rank test]] or [[Sign test]].
3. **"Is the number of daily failures Poisson?"** → [[Pearson chi-squared GOF test]] with $\lambda$ estimated, so $df=k-2$.
4. **"Two judges rank 10 wines."** → [[Kendall tau]] (or [[Spearman correlation]]).
5. **"Do income and expense correlate, or is it household size?"** → [[Partial correlation]].
6. **"Effect of diet and gender on weight loss."** → [[Two-way ANOVA]]; test the interaction first.

Related: [[Hypothesis testing]] · [[Parametric vs nonparametric methods]]
