---
tags: [ma012, lecture/L04, type/concept, exam/4]
aliases: [ECDF, Empirical distribution function, Empirická distribuční funkce]
lecture: L04
exam: 4
---

# Empirical cumulative distribution function (ECDF)

> [!info] Definition
> $$\hat F(x)=\frac1n\sum_{i=1}^n\mathbb 1\{X_i\le x\},\qquad \mathbb 1\{X_i\le x\}=\begin{cases}1&X_i\le x\\0&X_i>x\end{cases}$$
> In words: **the proportion of observations $\le x$**.

## Interpretation and properties
- It is the sample estimate of the CDF $F(x)=P(X\le x)$ → [[Random variable and distribution]].
- **Shape:**
  - a right-continuous, non-decreasing **step function** from 0 to 1;
  - it jumps by $\frac1n$ at each observation (by $\frac rn$ at a value repeated $r$ times).
- **Exact distribution at a fixed $x$:** $n\hat F(x)\sim\mathrm{Bin}(n,F(x))$, so
  - $E\hat F(x)=F(x)$ (unbiased);
  - $\mathrm{Var}\,\hat F(x)=\frac{F(x)(1-F(x))}n\to0$.
- **Glivenko–Cantelli:** $\sup_x\lvert\hat F(x)-F(x)\rvert\to0$, i.e. the ECDF converges to the true CDF **uniformly**. This is the basis of the [[Kolmogorov-Smirnov test]].
- **Reading the graph:**
  - the height at $x$ = the share of data $\le x$;
  - the $x$ where the height crosses 0.5 is the sample median;
  - steep parts mean many observations, flat parts few.

```r
Fn <- ecdf(x); Fn(20)                  # proportion <= 20
plot(Fn); curve(pexp(x, rate = 0.04), add = TRUE, col = "red")
ggplot(dat, aes(x)) + stat_ecdf()
```

Related: [[Kolmogorov-Smirnov test]] · [[Binomial distribution]] · [[Order statistics and ranks]]
