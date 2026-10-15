---
tags: [ma012, lecture/L04, type/concept]
aliases: [PIT, Probability integral transformation, Inverse transform method]
lecture: L04
---

# Probability integral transform

> [!important] Theorem
> If $X$ is continuous with CDF $F$, then $Y=F(X)\sim U(0,1)$.

**Proof** (for strictly increasing $F$ and $0<y<1$):
$$P(Y\le y)=P(F(X)\le y)=P\big(X\le F^{-1}(y)\big)=F\big(F^{-1}(y)\big)=y$$
This is the CDF of $U(0,1)$.

## Why it matters
- **Distribution-free GOF tests:** under $H_0$, the values $F_0(X_i)$ are uniform whatever $F_0$ is. So the null distributions of the [[Kolmogorov-Smirnov test]], Cramér–von Mises and Anderson–Darling statistics are the **same for every continuous $F_0$**, and one table serves all.
- **Inverse transform method:** run it backwards. If $U\sim U(0,1)$, then $X=F^{-1}(U)\sim F$. This is how random numbers from any distribution are generated.
- **The rank analogue:** ranks are uniform on the permutations → [[Order statistics and ranks]].

```r
x <- rgamma(1e4, shape = 2, rate = 1)   # any continuous distribution
y <- pgamma(x, shape = 2, rate = 1)
hist(y)                                  # flat
ks.test(y, "punif")                      # not rejected
qexp(runif(5), rate = 0.04)              # inverse transform: Ex(0.04) numbers
```

Related: [[Empirical CDF]] · [[Random variable and distribution]]
