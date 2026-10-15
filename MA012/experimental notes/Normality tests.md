---
tags: [ma012, lecture/L04, type/test, exam/4]
aliases: [Tests of normality, D'Agostino test, Anscombe-Glynn test, Jarque-Bera test, Anderson-Darling test, Cramér-von Mises test]
lecture: [L01, L02, L04]
exam: 4
---

# Normality tests

**Hypotheses:** $H_0$: the sample comes from a normal distribution ($\mu,\sigma$ unknown); $H_1$: it doesn't.

> [!important] Names to know (exam topic 4)
> D'Agostino · Anscombe–Glynn · Jarque–Bera · **Shapiro–Wilk** · Cramér–von Mises · Anderson–Darling · **Lilliefors** · Pearson χ²

| Test | Based on | Null distribution | R |
|---|---|---|---|
| D'Agostino (1970) | transformed **skewness** | $N(0,1)$ | `moments::agostino.test` |
| Anscombe–Glynn (1983) | transformed **kurtosis** | $N(0,1)$ | `moments::anscombe.test` |
| Jarque–Bera (1980) | skewness **and** kurtosis | $\chi^2(2)$ | `moments::jarque.test` |
| [[Shapiro-Wilk test]] (1965) | order statistics, i.e. Q-Q straightness | special tables | `shapiro.test` |
| Cramér–von Mises (1928) | integrated squared ECDF gap | special | `nortest::cvm.test` |
| Anderson–Darling (1952) | same, but **tail-weighted** | special | `nortest::ad.test` |
| [[Lilliefors test]] (1967) | K-S with $\bar X,S$ | Lilliefors tables | `nortest::lillie.test` |
| [[Pearson chi-squared GOF test]] | binned counts | $\chi^2$ | `nortest::pearson.test` |

## Formulas (for understanding)
**Jarque–Bera**, with $b_1$ = skewness and $b_2$ = **excess** kurtosis → [[Skewness and kurtosis]]:
$$JB=\frac n6\left(b_1^2+\frac{b_2^2}4\right)\overset{as.}{\sim}\chi^2(2)$$

**ECDF-distance family:** $n\int[\hat F(x)-F_0(x)]^2\,w(F_0(x))\,dF_0(x)$, with the weight
- $w\equiv1$ → **Cramér–von Mises**:
$$T=\frac1{12n}+\sum_{i=1}^n\left[\frac{2i-1}{2n}-F_0(X_{(i)})\right]^2$$
- $w(u)=\frac1{u(1-u)}$, which is large near 0 and 1 → **Anderson–Darling**, sensitive in the **tails**:
$$A^2=-n-\frac1n\sum_{i=1}^n(2i-1)\left[\ln F_0(X_{(i)})+\ln\big(1-F_0(X_{(n+1-i)})\big)\right]$$

## Practical guidance (lectures 2 and 4)
- **Shapiro–Wilk:** the standard choice, high power, $3\le n\le5000$.
- **Anderson–Darling:** good for $n\ge50$ and for tail problems.
- **Lilliefors:** needs $n\ge100$ and has weak power.
- **Always look at a [[Q-Q plot]] too:**
  - with large $n$, every test rejects trivial deviations;
  - with small $n$, real deviations pass unnoticed.

## Example: average restart time ($n=246$)
| Test | Statistic | p |
|---|---|---|
| D'Agostino | skew = 1.67, z = 8.02 | $10^{-15}$ |
| Anscombe–Glynn | kurt = 8.40, z = 6.08 | $10^{-9}$ |
| Jarque–Bera | 414.1 | $<2.2\cdot10^{-16}$ |
| Shapiro–Wilk | W = 0.888 | $1.7\cdot10^{-12}$ |
| Cramér–von Mises | 0.675 | $9.5\cdot10^{-8}$ |
| Anderson–Darling | 4.38 | $6.7\cdot10^{-11}$ |
| Lilliefors | D = 0.114 | $3.6\cdot10^{-8}$ |
| Pearson χ² | 49.0 | $3.3\cdot10^{-5}$ |

**Conclusion:** all tests reject. The data are right-skewed with a heavy right tail.

**Check by hand:** $JB=\frac{246}6(1.674^2+5.403^2/4)=41(2.80+7.30)=414$ ✔.

Related: [[Skewness and kurtosis]] · [[Kolmogorov-Smirnov test]] · [[ANOVA assumptions and diagnostics]]
