---
tags: [ma012, lecture/L01, type/distribution]
aliases: [Gaussian distribution, N(mu sigma2), Standard normal, Standardization]
lecture: L01
---

# Normal (Gaussian) distribution $N(\mu,\sigma^2)$

$$f(x)=\frac1{\sqrt{2\pi}\,\sigma}\exp\!\left(-\frac{(x-\mu)^2}{2\sigma^2}\right),\qquad EX=\mu,\;\mathrm{Var}X=\sigma^2$$

**Shape:**
- symmetric bell around $\mu$, with mean = median = mode;
- skewness 0 and kurtosis 3 (excess kurtosis 0) → [[Skewness and kurtosis]].

## Standardization
$$Z=\frac{X-\mu}{\sigma}\sim N(0,1)$$
- $\Phi$ denotes the CDF of $N(0,1)$, and $u_p=\Phi^{-1}(p)$ its quantiles.
- $u_{0.975}\approx1.96$, $u_{0.95}\approx1.645$, $u_{0.995}\approx2.576$.

## Facts used throughout the course
1. **Sums and means:** if $Y_i$ are i.i.d. $N(\mu,\sigma^2)$, then
   - $\sum(Y_i-\mu)\sim N(0,n\sigma^2)$,
   - so $\bar Y\sim N(\mu,\sigma^2/n)$ and $\frac{\bar Y-\mu}{\sigma}\sqrt n\sim N(0,1)$.
2. **Squares:** a sum of $k$ squared independent $N(0,1)$ variables is $\chi^2(k)$ → [[Chi-squared distribution]].
3. **Unknown $\sigma$:** replacing $\sigma$ by $S$ gives a $t$ distribution → [[Student t distribution]].
4. **Asymptotic normality:** many statistics (sign test, Wilcoxon, Mann-Whitney, Fisher z) are approximately normal for large $n$ → [[Central limit theorem]].

## Where normality is assumed
- [[t-tests]], [[One-way ANOVA]], [[Two-way ANOVA]] (the errors $\varepsilon\sim N(0,\sigma^2)$);
- tests of [[Pearson correlation]] (bivariate normal).

How to check it: [[Q-Q plot]], [[Normality tests]], [[Shapiro-Wilk test]].

```r
dnorm(x, mean, sd); pnorm(q, mean, sd); qnorm(0.975); rnorm(n, mean, sd)
```

> [!warning] R uses the standard deviation
> `rnorm(n, 3, 2)` means $\sigma=2$, i.e. $\sigma^2=4$. The lecture writes $N(3,2^2)$.

Related: [[Bivariate normal distribution]] · [[Random variable and distribution]]
