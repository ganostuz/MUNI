---
tags: [ma012, lecture/L03, type/test]
aliases: [Permutation test, Randomization test, Exact test]
lecture: L03
---

# Permutation tests

- **What they are:**
  - a subset of nonparametric tests;
  - they use the **exact** null distribution of *any* test statistic;
  - they build it by computing the statistic for **all rearrangements** of the observed data.
- **History:** Fisher and Pitman, 1930s.
- **How the data are reused:** resampling **without replacement**, i.e. only permutations of the original sample.

## Algorithm
1. Choose a statistic $T$ that measures the effect, e.g. $T=\bar Y-\bar X$.
2. Compute the observed value $T^*$.
3. Under $H_0$ the group labels are **exchangeable**, so compute $T$ for every relabelling of the pooled data.
4. The p-value is the share of relabellings at least as extreme as $T^*$:
   - $P(\lvert T\rvert\ge\lvert T^*\rvert)$ for two-sided $H_1$;
   - $P(T\ge T^*)$ or $P(T\le T^*)$ for one-sided.

## Two-sample example
- **Setup:** $X$ ($m$ values) and $Y$ ($n$ values); $H_0:F_X=F_Y$, $H_1:F_Y>F_X$.
- **Pool them:** $Z=(X,Y)$ with $N=m+n$.
- **Count the splits:** there are $\binom Nn$ ways to split $Z$ into groups of sizes $m$ and $n$, each with probability $1/\binom Nn$ under $H_0$.
- **Exact p-value:**
$$p=\frac{\#\{T_i\ge T^*\}}{\binom Nn}$$
- **Approximate (Monte Carlo) p-value** with $B\ge999$ random permutations:
$$p\approx\frac{1+\#\{T_i\ge T^*\}}{B+1}$$

```r
x <- c(5.7,5.5,4.3,5.9,5.2,5.6,5.8,5.1); y <- c(5.0,4.5,4.2,5.4,4.4)
z <- c(x, y); m <- length(x); T_obs <- mean(x) - mean(y)
B <- 9999
T_perm <- replicate(B, { idx <- sample(length(z), m); mean(z[idx]) - mean(z[-idx]) })
(1 + sum(abs(T_perm) >= abs(T_obs))) / (B + 1)        # two-sided Monte Carlo p-value
# exact: all choose(13, 8) = 1287 splits
splits <- combn(length(z), m)
T_all  <- apply(splits, 2, function(i) mean(z[i]) - mean(z[-i]))
mean(abs(T_all) >= abs(T_obs) - 1e-12)
```

## Pros and cons
**Advantages:**
- exist for **any** statistic;
- no distributional assumptions;
- ideal for small $n$;
- useful when a standard error is hard to derive.

**Limitations:**
- computationally intensive;
- ad hoc;
- give mainly a p-value;
- require **exchangeability**: when testing a location difference, the variances must be equal.

> [!important] Permutation ≠ bootstrap
> - Permutation tests resample **without** replacement.
> - The [[Bootstrap]] resamples **with** replacement.

**Connection:** the rank tests ([[Mann-Whitney-Wilcoxon test]], [[Wilcoxon signed-rank test]]) are permutation tests applied to ranks. That is why their exact tables exist.

Related: [[Parametric vs nonparametric methods]]
