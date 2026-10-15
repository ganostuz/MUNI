---
tags: [ma012, moc]
aliases: [Errata, Slide corrections]
---

# Slide errata (found while checking every formula)

Learn the **corrected** versions. Each correction is also flagged in the relevant note.

| Lecture | Slide | On the slide | Correct |
|---|---|---|---|
| L01 | 22 | $V$ result written as "$W\sim\chi^2(n-1)$" | $V\sim\chi^2(n-1)$ ($W\sim\chi^2(n)$) → [[Chi-squared distribution]] |
| L01 | 38 | two-sample t-test: $T\sim t(n-1)$ | pooled: $t(n_X+n_Y-2)$; Welch: $t(\nu)$ with Welch–Satterthwaite $\nu$ → [[t-tests]] |
| L02 | 4 | $\bar Y_{i\cdot}=\frac1{n_i}\sum_{j=1}^{k}Y_{ij}$ | upper index $n_i$; likewise in $\bar Y_{\cdot\cdot}$ |
| L02 | 6 | $H_0:\mu_1=\dots=\mu_k=0$ | $H_0:\mu_1=\dots=\mu_k$ → [[One-way ANOVA]] |
| L02 | 10 | $SS_A/\sigma^2\sim\chi^2(d_T=k-1)$ | $d_A=k-1$ |
| L02 | 17 | "$\tau_i$ = the difference of $\mu_i$ from $\mu_i$" | from $\mu_1$ → [[ANOVA parametrizations]] |
| L03 | 6 | column sums 54 and 65 | **50** (competition) and **60** (modified competition) → [[Order statistics and ranks]] |
| L03 | 9 | "test statistic $D$" | the statistic is $U$ → [[Sign test]] |
| L04 | 19 | title $\mathrm{Ex}(\lambda=0.1)$ | the test uses $\lambda=0.04$ → [[Kolmogorov-Smirnov test]] |
| L04 | 20 | K-S critical value (garbled) | reject if $\sqrt nD\ge\sqrt{\tfrac12\ln(2/\alpha)}$ (= 1.36 for α = 0.05) |
| L04 | 23 | two-sample K-S scaled by $\sqrt n$ | scale by $\sqrt{nm/(n+m)}$ |
| L04 | 25 | $\mathrm{Var}\,b_2$ without the factor $n$ | $\frac{24n(n-2)(n-3)}{(n+1)^2(n+3)(n+5)}\approx\frac{24}n$ → [[Skewness and kurtosis]] |
| L04 | 26 | table of skewness/kurtosis values | looks sample-based: exact $LN(1,1.1)$ skewness ≈ 8.2; learn the pattern, not the digits |
| L04 | 29 | CvM term $\frac{2i-1}{n}$ | $\frac{2i-1}{2n}$ → [[Normality tests]] |
| L05 | 4 | Karl Pearson (1985) | 1895 |
| L05 | 20 | $\mathbf S$ is positive definite | positive **semi**definite in general (definite if no exact linear dependence and $n>m$) |
| L05 | 31 | "If nt ties are present" | "If **no** ties are present" → [[Kendall tau]] |

Everything else, including all worked examples, was recomputed and matches the slides.
