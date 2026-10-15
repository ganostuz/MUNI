---
tags: [ma012, type/distribution]
aliases: [Bin(n p), Binomial, Bernoulli distribution, de Moivre-Laplace]
lecture: [L03, L04]
---

# Binomial distribution $\mathrm{Bin}(n,p)$

**What it counts:** the number of successes in $n$ independent trials with success probability $p$. (One trial is the **Bernoulli** distribution.)

$$P(X=k)=\binom nk p^k(1-p)^{n-k},\quad k=0,\dots,n;\qquad EX=np,\;\mathrm{Var}X=np(1-p)$$

## de Moivre–Laplace theorem
This is the [[Central limit theorem]] for the binomial: for large $n$,
$$\frac{X-np}{\sqrt{np(1-p)}}\overset{as.}{\sim}N(0,1).$$

## Where it appears
- **[[Sign test]]:** under $H_0$, $S^+\sim\mathrm{Bin}(n,\tfrac12)$, with $ES^+=n/2$ and $\mathrm{Var}S^+=n/4$. For large $n$, $U=\frac{2S^+-n}{\sqrt n}\approx N(0,1)$.
- **[[Pearson chi-squared GOF test]]:**
  - each cell count $N_j\sim\mathrm{Bin}(n,p_j)$ (a margin of the [[Multinomial distribution]]);
  - Example 1 there tests $H_0:\mathrm{Bin}(5,0.5)$ for the number of boys among 5 children.
- **ECDF:** $n\hat F(x)\sim\mathrm{Bin}(n,F(x))$ → [[Empirical CDF]].
- **[[Logistic regression]]:** binomial response data.

```r
dbinom(0:5, size = 5, prob = 0.5)       # 1/32, 5/32, 10/32, 10/32, 5/32, 1/32
pbinom(2, size = 10, prob = 0.5)        # P(S+ <= 2) = 0.0547
binom.test(2, 10, p = 0.5)              # exact sign test, p = 0.1094
```

Related: [[Random variable and distribution]]
