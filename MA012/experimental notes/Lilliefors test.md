---
tags: [ma012, lecture/L04, type/test, exam/4]
aliases: [Lilliefors, Lilliefors normality test]
lecture: L04
exam: 4
---

# Lilliefors test

**Hypotheses:** $H_0$: the data come from $N(\mu,\sigma^2)$ with $\mu,\sigma^2$ **unknown**; $H_1$: they do not.

## Steps
1. Estimate $\hat\mu=\bar X$ and $\hat\sigma=S$.
2. Compute the K-S distance to the **fitted** normal:
$$D=\sup_x\left\lvert\hat F(x)-\Phi\!\left(\frac{x-\bar X}{S}\right)\right\rvert$$
3. Reject if $\sqrt nD\ge L_\alpha(n)$, using the **Lilliefors critical values** (Lilliefors 1967; corrected by Dallal & Wilkinson 1986).

## Why it is not just K-S
- The parameters are fitted to the **same** data, so the fitted normal CDF "hugs" the ECDF and $D$ comes out systematically **smaller**.
- With the ordinary K-S critical values the test would be far too conservative: it would almost never reject.
- The Lilliefors critical values are therefore **smaller**.
- **Same logic as** the "$-m$" degrees of freedom in the [[Pearson chi-squared GOF test]].

**Simulation that shows it:**
```r
set.seed(1)
mean(replicate(2000, { x <- rnorm(50); ks.test(x, "pnorm", mean(x), sd(x))$p.value }) < 0.05)  # ~0 (wrong)
mean(replicate(2000, { x <- rnorm(50); nortest::lillie.test(x)$p.value }) < 0.05)              # ~0.05 (correct)
```

**Power:** weaker than Shapiro–Wilk or Anderson–Darling; the lecture suggests $n\ge100$.

**Example (average restart time):** $D=0.114$, $p=3.6\cdot10^{-8}$ → reject normality.

Related: [[Kolmogorov-Smirnov test]] · [[Normality tests]] · [[Shapiro-Wilk test]]
