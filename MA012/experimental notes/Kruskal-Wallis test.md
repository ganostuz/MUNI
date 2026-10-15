---
tags: [ma012, lecture/L03, type/test, exam/2]
aliases: [Kruskal Wallis, KW test, Nonparametric ANOVA]
lecture: L03
exam: 2
---

# Kruskal–Wallis test

**History:** Kruskal & Wallis (1952).
**What it is:**
- the nonparametric analogue of [[One-way ANOVA]];
- the extension of the [[Mann-Whitney-Wilcoxon test]] to $a\ge2$ independent groups.

## Model
- **Data:** $Y$ at least ordinal, plus a grouping factor $A$ with $a$ levels.
- **Groups:** group $i$, $(Y_{i1},\dots,Y_{in_i})$, is a random sample from a continuous distribution with CDF $F_i$.
- **Independence:** all samples are mutually independent.

## Hypotheses
- $H_0:F_1(x)=\dots=F_a(x)$ for all $x$. This implies **equal mean ranks** in all groups.
- $H_1$: at least one sample **stochastically dominates** another: $F_i(x)>F_j(x)$ or $F_i(x)<F_j(x)$ for all $x$.
- **Equal variances** (equal shapes) are still needed; they are part of $H_0$.

## Algorithm
1. Pool all $n=\sum n_i$ observations and assign (mid)ranks $R_{ij}$.
2. Compute the rank sum of each group: $T_i=\sum_jR_{ij}$ (check: $\sum T_i=n(n+1)/2$).
3. Compute
$$Q=\frac{12}{n(n+1)}\sum_{i=1}^a\frac{T_i^2}{n_i}-3(n+1)$$

## Decision
- **Exact:** reject if $Q\ge h_\alpha(a-1)$ (table).
- **Asymptotic:** $Q\overset{as.}{\sim}\chi^2(a-1)$ with $EQ=a-1$; reject if $Q\ge\chi^2_{1-\alpha}(a-1)$.
- The $\chi^2$ approximation can be poor if any $n_i<5$.

**Tie correction** (recommended with >25 % ties):
$$Q_{adj}=\frac QC,\qquad C=1-\frac{\sum_k m_k(m_k^2-1)}{n(n^2-1)},\qquad m_k=\text{size of the }k\text{-th tie group}$$

**After a rejection:** run post-hoc tests, e.g. pairwise [[Mann-Whitney-Wilcoxon test]]s with the Bonferroni correction → [[Post-hoc multiple comparisons]].

## Example: weight of potatoes per plant, 4 varieties ($n=15$)
| Variety | weights | ranks | $n_i$ | $T_i$ |
|---|---|---|---|---|
| 1 | 0.9, 0.8, 0.6, 0.9 | 3.5, 2, 1, 3.5 | 4 | 10 |
| 2 | 1.3, 1.0, 1.3 | 11, 5.5, 11 | 3 | 27.5 |
| 3 | 1.3, 1.5, 1.6, 1.1, 1.5 | 11, 13.5, 15, 7.5, 13.5 | 5 | 60.5 |
| 4 | 1.1, 1.2, 1.0 | 7.5, 9, 5.5 | 3 | 22 |

1. **Check:** $\sum T_i=120=15\cdot16/2$ ✔.
2. $\sum T_i^2/n_i=25+252.08+732.05+161.33=1170.47$.
3. $Q=\frac{12}{240}\cdot1170.47-48=10.52$.
4. **Ties:** four pairs (0.9, 1.0, 1.1, 1.5) and one triple (1.3), so $\sum m(m^2-1)=4\cdot6+24=48$ and $C=1-48/3360=0.9857$.
5. $Q_{adj}=10.676>\chi^2_{0.95}(3)=7.815$ → **reject $H_0$**, $p=0.014$.
6. **Post-hoc letters:** 3 *a*, 2 *ab*, 4 *b*, 1 *c*.

```r
dat <- data.frame(Weight = c(0.9,0.8,0.6,0.9, 1.3,1.0,1.3, 1.3,1.5,1.6,1.1,1.5, 1.1,1.2,1.0),
                  Variety = factor(rep(1:4, c(4,3,5,3))))
kruskal.test(Weight ~ Variety, data = dat)      # chi-squared = 10.676, df = 3, p = 0.01361
with(dat, agricolae::kruskal(Weight, Variety))  # with letters
pairwise.wilcox.test(dat$Weight, dat$Variety, p.adjust.method = "bonferroni")
```

## Kruskal-Wallis vs one-way ANOVA (a typical exam question)
| | [[One-way ANOVA]] | Kruskal–Wallis |
|---|---|---|
| Data | numeric (interval/ratio) | at least ordinal |
| Assumption | normal errors, equal variances | continuous distributions of the same shape |
| $H_0$ | equal **means** $\mu_1=\dots=\mu_k$ | equal **distributions** (equal mean ranks) |
| Statistic | $F=MS_A/MS_e$ on the values | $Q$ on the ranks (≈ "ANOVA on ranks") |
| Distribution | $F(k-1,n-k)$ | $\chi^2(a-1)$ asymptotically |
| Post-hoc | Tukey, Bonferroni, Scheffé | pairwise MWW + Bonferroni |
| Better when | data are normal (more power) | small skewed samples, outliers, ordinal data |

Related: [[Median test]] · [[Van der Waerden test]] · [[Parametric vs nonparametric methods]]
