---
tags: [ma012, lecture/L04, type/test, exam/4]
aliases: [K-S test, KS test, Kolmogorov-Smirnov, Two-sample K-S test, Smirnov test]
lecture: L04
exam: 4
---

# Kolmogorov–Smirnov test

## One-sample test (Kolmogorov, 1933)
**Assumptions:**
- a random sample from a **continuous** distribution;
- $F_0$ **fully specified**: no parameters estimated from the same data;
- no ties.

**Hypotheses:** $H_0:F(x)=F_0(x)$ for all $x$; $H_1:F(x)\ne F_0(x)$ for some $x$.

$$D=\sup_x\big\lvert\hat F(x)-F_0(x)\big\rvert$$

> [!important] Geometric meaning
> $D$ is the **largest vertical distance** between the ECDF staircase and the hypothesised CDF curve.

**How to compute it:**
- The gap can only be largest right at a jump.
- Just before the $i$-th jump the ECDF equals $\frac{i-1}n$; just after, $\frac in$.
- So check both values at each ordered observation:
$$D=\max_i\max\left\{\frac in-F_0(X_{(i)}),\;F_0(X_{(i)})-\frac{i-1}n\right\}$$

**Decision:** reject if $\sqrt n\,D\ge D_\alpha$.
- For $n\ge30$, the asymptotic critical value is $D_\alpha\approx\sqrt{\tfrac12\ln\tfrac2\alpha}$.
- Equivalently, reject if $D\ge\sqrt{\ln(2/\alpha)/(2n)}$.

| $\alpha$ | 0.10 | 0.05 | 0.01 |
|---|---|---|---|
| critical value for $\sqrt nD$ | 1.22 | **1.36** | 1.63 |

**Distribution-free:** the null distribution of $D$ is the same for every continuous $F_0$, because $F_0(X_i)\sim U(0,1)$ → [[Probability integral transform]].

## Example: daily total restart time of a network device ($n=246$), $H_0:\mathrm{Ex}(0.04)$
```r
ks.test(dat$time_restart, "pexp", rate = 0.04)   # D = 0.0827, p = 0.069 + warning about ties
```

1. **By hand:** $\sqrt{246}\cdot0.0827=1.297$.
   - At 5 %: $1.297<1.36$ → **not rejected**.
   - At 10 %: $1.297>1.22$ → it would be rejected. The result is borderline.
2. **The ties warning:** many days had a restart time of exactly 0. The data are not continuous, so the test is only approximate.
3. **Exponential [[Q-Q plot]]:** the top points lie below the line, so the right tail is lighter than the exponential model predicts.

> [!warning] Slide title
> It says $\lambda=0.1$, but the test uses $\lambda=0.04$ → [[00 Slide errata]].

## Two-sample test (Smirnov, 1939)
**Setup:** two independent samples from continuous distributions.
**Hypotheses:** $H_0:F_X=F_Y$; $H_1:F_X\ne F_Y$.

$$D=\sup_x\lvert\hat F_X(x)-\hat F_Y(x)\rvert$$

- **Meaning:** the largest vertical gap between the **two ECDFs**.
- **Decision** (large samples, $m+n\ge35$): reject if $\sqrt{\frac{nm}{n+m}}D\ge\sqrt{\tfrac12\ln\tfrac2\alpha}$.
- A rejection means the distributions differ *somewhere* (location, spread or shape). Look at the ECDF plot to see where.

```r
ks.test(x, y)
plot(ecdf(x)); plot(ecdf(y), add = TRUE, col = "blue")
```

## Usage and limits
- **Use it for:** a **continuous**, fully specified $F_0$; or comparing two samples.
- **With estimated parameters**, the K-S p-values are far too large (the test almost never rejects). For normality use the [[Lilliefors test]] or the [[Shapiro-Wilk test]].
- **Discrete data or ties:** use the [[Pearson chi-squared GOF test]] instead.
- **Power:** less powerful than Shapiro–Wilk or Anderson–Darling for normality; most sensitive in the middle of the distribution, not in the tails.

Related: [[Empirical CDF]] · [[Normality tests]] · [[L04 Goodness-of-fit tests]]
