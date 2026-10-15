---
tags: [ma012, lecture/L01, lecture/L05, type/concept]
aliases: [Random variable, Probability distribution, PDF, PMF, CDF, Quantile]
lecture: [L01, L05]
---

# Random variable and probability distribution

> [!info] Definitions
> - A **random variable** $X:\Omega\to\mathbb R$ is a measurable map from the outcomes of an experiment to numbers.
> - A **realization** $x$ is the value actually observed.
> - The **probability distribution** $P$ assigns probabilities to events (sets of values of $X$).

## Describing a distribution
| | Discrete $X$ | Continuous $X$ |
|---|---|---|
| point probabilities | PMF $p(x)=P(X=x)$ | $P(X=x)=0$ |
| density | — | PDF $f(x)\ge0$, $\int f=1$ |
| CDF $F(x)=P(X\le x)$ | step function | continuous, $F'(x)=f(x)$ |
| $P(a<X\le b)$ | $\sum_{a<x\le b}p(x)$ | $\int_a^b f(x)\,dx=F(b)-F(a)$ |
| mean $E X$ | $\sum x\,p(x)$ | $\int x f(x)\,dx$ |
| variance | $\mathrm{Var}X=E[X-EX]^2$ | same |

**Properties of the CDF:** it is non-decreasing and right-continuous, with $F(-\infty)=0$ and $F(\infty)=1$.

**Quantile.** The $p$-quantile $q_p$ satisfies $F(q_p)=p$ (for continuous $F$: $q_p=F^{-1}(p)$).
- On a density plot, $q_{0.95}$ is the point with **area 0.95 to its left**.
- The median is $q_{0.5}$; the quartiles are $q_{0.25}$ and $q_{0.75}$.

## R naming convention
For a distribution with R name `xxx` (`norm`, `t`, `chisq`, `f`, `binom`, `exp`, `pois`, `signrank`, `wilcox`, ...):

| Prefix | Gives | Example |
|---|---|---|
| `d` | density or PMF | `dnorm(0)` |
| `p` | CDF $F(x)$ | `pt(2, df = 5)` |
| `q` | quantile $F^{-1}(p)$ | `qchisq(0.95, df = 3)` = 7.815 |
| `r` | random numbers | `rexp(10, rate = 0.04)` |

Upper tail: `pchisq(K, df, lower.tail = FALSE)` = $P(X\ge K)$. This is how most p-values are computed.

## Common distributions in this course
- **Discrete:** Bernoulli, [[Binomial distribution]], [[Multinomial distribution]], Poisson, geometric, hypergeometric, negative binomial.
- **Continuous:** uniform, [[Normal distribution]], [[Chi-squared distribution]], [[Student t distribution]], [[F distribution]], [[Exponential distribution]], lognormal, gamma, Weibull, beta, logistic.
- **Multivariate:** [[Bivariate normal distribution]].

Related: [[Random sample]] · [[Empirical CDF]] · [[Probability integral transform]]
