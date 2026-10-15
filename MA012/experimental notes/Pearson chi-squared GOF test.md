---
tags: [ma012, lecture/L04, type/test, exam/3]
aliases: [Chi-squared goodness-of-fit test, Pearson's chi-squared test, Chi2 GOF, Observed and expected frequencies]
lecture: L04
exam: 3
---

# Pearson's chi-squared goodness-of-fit test (Pearson, 1900)

## Setup
- Split the possible values into $k$ disjoint categories $A_1,\dots,A_k$.
- $p_j=P(X\in A_j)$ under $H_0$.
- **Observed (empirical) frequencies** $N_j$ = number of observations in $A_j$. They are random, and $(N_1..N_k)\sim\mathrm{Mult}_k(n;p_1..p_k)$ → [[Multinomial distribution]].
- **Expected (theoretical) frequencies:** $n_j=np_j$.

> [!warning] Notation
> Capital $N_j$ = **observed**; small $n_j$ = **expected**.

## Statistic
**Step 1.** Each $N_j\sim\mathrm{Bin}(n,p_j)$. By de Moivre–Laplace,
$$U_j=\frac{N_j-n_j}{\sqrt{n_j}}\approx N(0,1).$$

**Step 2.** Sum the squares:
$$K=\sum_{j=1}^k\frac{(N_j-n_j)^2}{n_j}=\sum_{j=1}^k\frac{N_j^2}{n_j}-n=\frac1n\sum_{j=1}^k\frac{N_j^2}{p_j}-n$$

**Reading $K$:**
- Small $K$ means a good fit.
- Large $K$ is evidence **against** $H_0$, so the test is **right-tailed**.

> [!important] Theorem
> Under $H_0$: $K\overset{as.}{\sim}\chi^2(k-1-m)$, where $m$ = the number of parameters **estimated** from the data.
> Reject $H_0$ if $K\ge\chi^2_{1-\alpha}(k-1-m)$.

**Where the df come from:**
- **−1** because the counts are tied by $\sum N_j=n$;
- **−m** because each estimated parameter pulls the model towards the data → [[Degrees of freedom]].

| $H_0$ | $m$ |
|---|---|
| fully specified, e.g. $\mathrm{Bin}(5,0.5)$ | 0 |
| Poisson or exponential with the rate estimated | 1 |
| normal with $\mu,\sigma^2$ estimated | 2 |

**Estimating parameters:**
- Theory: the **modified minimum χ²** method solves $\sum_j\frac{N_j}{p_j(\theta)}\frac{\partial p_j(\theta)}{\partial\theta_i}=0$, the ML equations for the binned data.
- Practice: plug in the ordinary MLE ($\hat\lambda=1/\bar X$, $\hat\lambda=\bar X$ for Poisson, $\bar X$ and $S$ for the normal).

## When the approximation is good (checked on the **expected** counts)
- **(a)** all $n_j\ge5$; or
- **(b) Yarnold:** let $q$ = the fraction of cells with $n_j<5$; then all $n_j\ge5q$ and no $n_j=0$.
- If neither holds, **merge adjacent categories**. This changes $k$ and hence the df.
- **Yates's correction** for small tables: $K=\sum\frac{(\lvert N_j-n_j\rvert-\frac12)^2}{n_j}$.

## Procedure
| Step | Discrete distribution | Continuous distribution |
|---|---|---|
| categories | the individual values (last one "$\ge c$" if the support is infinite) | intervals $(t_{j-1},t_j]$ covering **the whole support** |
| expected counts | $n_j=n\,P(X=x_j)$ | $n_j=n[F(t_j)-F(t_{j-1})]$ |
| number of intervals | — | $k\approx\sqrt n$ or $1+\log_2n$ |

**Then, in both cases:**
1. Estimate the parameters.
2. Compute $N_j$ and $n_j$.
3. Check (a) or (b); merge if needed.
4. Compute $K$, the df and $p$; decide.

## Example 1: boys in 84 five-child families, $H_0:\mathrm{Bin}(5,0.5)$
| boys | 0 | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|---|
| $N_j$ | 3 | 10 | 22 | 31 | 14 | 4 |
| $p_j=\binom5j/32$ | 1/32 | 5/32 | 10/32 | 10/32 | 5/32 | 1/32 |
| $n_j$ | 2.625 | 13.125 | 26.25 | 26.25 | 13.125 | 2.625 |

**Without merging:**
- $K=0.054+0.744+0.688+0.860+0.058+0.720=3.12$.
- $df=5$, critical value 11.07, $p=0.68$.
- R warns because two $n_j<5$. Yarnold is actually satisfied: $q=2/6$, $5q=1.67\le2.625$.

**After merging {0,1} and {4,5}:**
- $N=(13,22,31,18)$, $n=(15.75,26.25,26.25,15.75)$.
- $K=2.35$, $df=3$, critical value 7.81, $p=0.50$.

**Conclusion:** either way, **not rejected** — consistent with P(boy) = ½.

```r
N <- c(3, 10, 22, 31, 14, 4); p <- dbinom(0:5, 5, 0.5); E <- sum(N) * p
sum((N - E)^2 / E); pchisq(3.1238, 5, lower.tail = FALSE)
chisq.test(N, p = p)                                   # X-squared = 3.1238, df = 5, p = 0.681
chisq.test(c(13, 22, 31, 18), p = c(6, 10, 10, 6)/32)  # 2.349, df = 3, p = 0.503
```

## Example 2: waiting times of 70 clients, $H_0$: exponential ($\lambda$ estimated)
| interval | (0,3] | (3,6] | (6,9] | (9,12] | (12,15] | (15,18] | (18,21] | (21,∞) |
|---|---|---|---|---|---|---|---|---|
| $N_j$ | 14 | 16 | 10 | 9 | 8 | 5 | 3 | 5 |
| $n_j$ | 20.0 | 14.3 | 10.2 | 7.29 | 5.21 | 3.72 | 2.66 | 6.64 |

1. **Estimate:** $\bar X=8.914$ from the interval midpoints, so $\hat\lambda=0.112$ and $m=1$.
2. **Expected probabilities:** $p_j=e^{-\hat\lambda t_{j-1}}-e^{-\hat\lambda t_j}$; the last interval is $e^{-21\hat\lambda}$.
3. **Cover the whole support:** with a last interval of $(21,24]$, the $n_j$ summed to only 65.3. Make it $(21,\infty)$.
4. **Result:** $K=4.8$, $df=8-1-1=6$, critical value 12.59, $p=0.57$ → **not rejected**.

```r
breaks <- c(0, 3, 6, 9, 12, 15, 18, 21, Inf); N <- c(14, 16, 10, 9, 8, 5, 3, 5)
mid <- c(1.5, 4.5, 7.5, 10.5, 13.5, 16.5, 19.5, 22.5)
lambda <- 1 / (sum(N * mid) / sum(N))
E <- sum(N) * diff(pexp(breaks, lambda)); K <- sum((N - E)^2 / E)
pchisq(K, df = length(N) - 1 - 1, lower.tail = FALSE)
```

> [!warning] R pitfall
> `chisq.test(N, p = p)` always uses $df=k-1$. With estimated parameters, compute the p-value yourself with $df=k-1-m$.

**Common mistakes:**
- checking "≥ 5" on the observed counts instead of the expected ones;
- bins that don't cover the whole support;
- forgetting the $-m$.

Related: [[Chi-squared distribution]] · [[Contingency tables]] (the same statistic for independence) · [[L04 Goodness-of-fit tests]]
