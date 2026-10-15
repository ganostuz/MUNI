---
tags: [ma012, lecture/L01, type/concept]
aliases: [i.i.d., iid, Random sample]
lecture: [L01, L03]
---

# Random sample

> [!info] Definition
> A **random sample** of size $n$ is a random vector $(X_1,\dots,X_n)$ of **i.i.d.** (independent, identically distributed) random variables:
> - each $X_i$ has the **same distribution** $P$;
> - $X_1,\dots,X_n$ are **mutually independent**, i.e. $P(A\cap B)=P(A)P(B)$ for events determined by different $X_i$.

- The random sample is the cornerstone of frequentist statistics: classic tests assume the data form one.
- **Sample statistics** (e.g. $\bar X$, $S^2$, ranks, test statistics) are functions of the random sample, so they are **random variables** with their own distributions.
- **Independence** comes from the **design of the experiment** (random allocation, no repeated measures). It is checked by thinking about how the data were collected, not by a test.

Built from the random sample:
- the ordered sample, order statistics and ranks → [[Order statistics and ranks]];
- the empirical CDF → [[Empirical CDF]];
- a bivariate random sample (pairs $(X_i,Y_i)$) → [[Pearson correlation]], [[Paired vs two-sample design]].

Related: [[Random variable and distribution]] · [[Hypothesis testing]]
