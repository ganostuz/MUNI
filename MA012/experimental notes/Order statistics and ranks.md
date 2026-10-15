---
tags: [ma012, lecture/L03, type/concept, exam/2]
aliases: [Ordered sample, Order statistic, Ranks, Rank statistic, Ties, Average ranks]
lecture: L03
exam: 2
---

# Ordered sample, order statistics and ranks

Let $(X_1,\dots,X_n)$ be a [[Random sample]] of at least ordinal data.

> [!info] Definitions
> - **Ordered sample:** $(X_{(1)},\dots,X_{(n)})$ with $X_{(1)}\le X_{(2)}\le\dots\le X_{(n)}$.
> - **$i$-th order statistic:** $X_{(i)}$. Special cases: $X_{(1)}$ = minimum, $X_{(n)}$ = maximum.
> - **Rank** $R_i$ of $X_i$ = its position in the ordered sample. With no ties, $R_i=1+\#\{j:X_j<X_i\}$ and $X_i=X_{(R_i)}$.
> - **Ties:** observations with equal values.

## Properties (continuous distribution, hence no ties)
- $P(X_i=X_j)=0$, so $X_{(1)}<\dots<X_{(n)}$.
- **Density of $X_{(r)}$:**
$$f_{X_{(r)}}(x)=r\binom nr f(x)\,[F(x)]^{r-1}[1-F(x)]^{n-r}$$
- Order statistics are **neither independent nor identically distributed**.
- **Ranks:**
  - every permutation $(R_1,\dots,R_n)$ has probability $\frac1{n!}$;
  - so the distribution of the ranks **does not depend** on the distribution of the data. This makes rank tests **distribution-free**.
- **Fixed sum:** $\sum_{i=1}^nR_i=\dfrac{n(n+1)}2$.

## Ranking with ties
| Method | Formula | R `ties.method` |
|---|---|---|
| **average ranks** (midranks) | $1+\#\{X_j<X_i\}+\tfrac12\#\{j\ne i:X_j=X_i\}$ | `"average"` (default) |
| standard competition ("1224") | $1+\#\{X_j<X_i\}$ | `"min"` |
| modified competition ("1334") | $\#\{X_j\le X_i\}$ | `"max"` |
| dense ("1223") | consecutive integers | `dplyr::dense_rank` |
| random | ties broken at random | `"random"` |

> [!important] Only average ranks keep the sum $n(n+1)/2$
> That is why rank tests use average ranks.

## Example (slide 6, recomputed)
$X=(2.0, 1.8, 2.1, 2.4, 1.9, 2.1, 2.0, 1.8, 2.3, 2.1)$; sorted: 1.8, 1.8, 1.9, 2.0, 2.0, 2.1, 2.1, 2.1, 2.3, 2.4.

| $X_i$ | 2.0 | 1.8 | 2.1 | 2.4 | 1.9 | 2.1 | 2.0 | 1.8 | 2.3 | 2.1 | Σ |
|---|---|---|---|---|---|---|---|---|---|---|---|
| average | 4.5 | 1.5 | 7 | 10 | 3 | 7 | 4.5 | 1.5 | 9 | 7 | **55** |
| competition | 4 | 1 | 6 | 10 | 3 | 6 | 4 | 1 | 9 | 6 | **50** |
| modified c. | 5 | 2 | 8 | 10 | 3 | 8 | 5 | 2 | 9 | 8 | **60** |

> [!warning] Slide 6 errata
> The slide's column sums 54 and 65 are wrong; the correct sums are **50** and **60** (only the average ranks sum to 55 = 10·11/2) → [[00 Slide errata]].

**How to compute an average rank:** the three values 2.1 occupy positions 6, 7, 8, so each gets $(6+7+8)/3=7$.

```r
x <- c(2.0, 1.8, 2.1, 2.4, 1.9, 2.1, 2.0, 1.8, 2.3, 2.1)
sort(x); order(x)                           # ordered sample; ordering permutation
rank(x)                                     # average ranks
rank(x, ties.method = "min"); rank(x, ties.method = "max")
```

Used by: [[Wilcoxon signed-rank test]] · [[Mann-Whitney-Wilcoxon test]] · [[Kruskal-Wallis test]] · [[Spearman correlation]] · [[Kolmogorov-Smirnov test]] (via $X_{(i)}$) · [[Shapiro-Wilk test]]
