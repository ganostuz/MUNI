---
tags: [ma012, lecture/L03, type/test]
aliases: [Van der Waerden, Normal scores test]
lecture: L03
---

# Van der Waerden test (normal scores)

**Hypotheses:** the same as the [[Kruskal-Wallis test]].
**Idea:** convert each rank into a quantile of $N(0,1)$ (a "normal score"), then do an ANOVA-like comparison of the scores.

1. Normal scores: $A_{ij}=\Phi^{-1}\!\left(\dfrac{R_{ij}}{n+1}\right)$, where $R_{ij}$ is the rank in the pooled sample.
2. Group sums: $A_{i\cdot}=\sum_jA_{ij}$.
3. Statistic:
$$Q_W=\frac{\sum_i A_{i\cdot}^2/n_i}{\frac1{n-1}\sum_i\sum_jA_{ij}^2}\;\overset{as.}{\sim}\;\chi^2(a-1);\qquad\text{reject if }Q_W\ge\chi^2_{1-\alpha}(a-1).$$

**Example (potatoes):**
- $Q_W\approx10.51$, $p=0.015$ → reject.
- Same letters as Kruskal-Wallis: 3 *a*, 2 *ab*, 4 *b*, 1 *c*.

```r
with(dat, agricolae::waerden.test(Weight, Variety))   # Chisq = 10.51, p = 0.0147
```

**Why use it:** when the data are close to normal, it is often a little more powerful than Kruskal-Wallis, while keeping the robustness of a rank test.

Related: [[Kruskal-Wallis test]] · [[Normal distribution]] · [[Order statistics and ranks]]
