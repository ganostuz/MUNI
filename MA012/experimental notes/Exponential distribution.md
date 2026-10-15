---
tags: [ma012, lecture/L04, type/distribution]
aliases: [Ex(lambda), Exponential]
lecture: L04
---

# Exponential distribution $\mathrm{Ex}(\lambda)$

**What it models:** waiting times between random events (e.g. client arrivals, restart times).

$$f(x)=\lambda e^{-\lambda x},\quad F(x)=1-e^{-\lambda x}\quad(x\ge0);\qquad EX=\frac1\lambda,\;\mathrm{Var}X=\frac1{\lambda^2}$$

**Properties:**
- skewness 2 and kurtosis 9; it is the point (4, 9) on the Cullen–Frey plot → [[Skewness and kurtosis]];
- **memoryless:** $P(X>s+t\mid X>s)=P(X>t)$;
- **MLE** of the rate: $\hat\lambda=1/\bar X$.

## Probability of an interval (for binned tests)
$$P(a<X\le b)=e^{-\lambda a}-e^{-\lambda b},\qquad P(X>a)=e^{-\lambda a}$$

## Examples in the course (L04)
- **Waiting times of 70 clients:** [[Pearson chi-squared GOF test]] with $\lambda$ estimated ($m=1$), so $df=8-1-1=6$; $K=4.8$, $p=0.57$ → not rejected.
- **Restart times of a network device:** [[Kolmogorov-Smirnov test]] against $\mathrm{Ex}(0.04)$; $D=0.083$, $p=0.069$.

```r
pexp(q, rate = lambda); qexp(p, rate); rexp(n, rate)
diff(pexp(c(0, 3, 6, Inf), rate = 0.112))   # interval probabilities
```

> [!warning] R uses the rate
> `pexp(x, rate = 0.04)` means mean = 25.
