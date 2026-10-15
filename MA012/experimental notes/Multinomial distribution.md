---
tags: [ma012, lecture/L04, type/distribution]
aliases: [Mult(n p), Multinomial]
lecture: L04
---

# Multinomial distribution $\mathrm{Mult}_k(n;p_1,\dots,p_k)$

**What it counts:** throw $n$ independent observations into $k$ disjoint categories with probabilities $p_1,\dots,p_k$ ($\sum p_j=1$). The counts $(N_1,\dots,N_k)$ are multinomial:

$$P(N_1=n_1,\dots,N_k=n_k)=\frac{n!}{n_1!\cdots n_k!}\,p_1^{n_1}\cdots p_k^{n_k},\qquad \sum n_j=n.$$

## Properties
- **Margins:** $N_j\sim\mathrm{Bin}(n,p_j)$, so $EN_j=np_j$ and $\mathrm{Var}N_j=np_j(1-p_j)$ → [[Binomial distribution]].
- **Not independent:** the counts must add up to $n$, which makes them **negatively correlated**:
$$\mathrm{Cov}(N_i,N_j)=-np_ip_j,\qquad \mathrm{Corr}(N_i,N_j)=-\sqrt{\frac{p_ip_j}{(1-p_i)(1-p_j)}}\quad(i\ne j).$$
- The constraint $\sum N_j=n$ is why the [[Pearson chi-squared GOF test]] statistic has $k-1$ (not $k$) [[Degrees of freedom]].

**Where it appears:** observed frequencies in the [[Pearson chi-squared GOF test]] and in [[Contingency tables]].

```r
dmultinom(c(3, 10, 22, 31, 14, 4), prob = dbinom(0:5, 5, 0.5))
rmultinom(1, size = 84, prob = dbinom(0:5, 5, 0.5))
```
