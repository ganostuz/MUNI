---
tags: [ma012, lecture/L03, type/concept]
aliases: [Paired test, Paired comparison, Paired location test]
lecture: L03
---

# Paired vs two-sample design

| | **Paired** | **Two independent samples** |
|---|---|---|
| Data | $n$ i.i.d. **pairs** $(V_i,W_i)$, dependent within a pair (before/after, left/right, twins) | $(X_1..X_m)$ and $(Y_1..Y_n)$ independent, possibly $m\ne n$ |
| Reduce to | differences $X_i=W_i-V_i$ → **one-sample** test | compare two distributions |
| Parametric | paired [[t-tests]] | two-sample t-test / Welch |
| Nonparametric | [[Sign test]], [[Wilcoxon signed-rank test]] on the differences | [[Mann-Whitney-Wilcoxon test]] |

**Hypothesis for paired data** (e.g. paired sign test): $H_0:\tilde w-\tilde v=x_0$ vs $H_1:\tilde w-\tilde v\ne x_0$, where $\tilde{}$ denotes a median.

> [!warning] A classic exam trap
> Analysing paired data as two independent samples throws away the pairing. The between-subject variability then stays in the error term, and the power drops a lot. The design decides the test, not the data values.

```r
wilcox.test(after, before, paired = TRUE)    # = wilcox.test(after - before)
binom.test(sum(after - before > 0), sum(after != before))   # paired sign test
```
