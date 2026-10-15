---
tags: [ma012, lecture/L03, type/test]
aliases: [Mood's median test, Brown-Mood median test]
lecture: L03
---

# Median test (Mood's / Brown–Mood)

**Hypotheses:**
- $H_0$: all $a$ groups have the **same median**;
- $H_1$: at least two medians differ.

**Idea:** for each group, count how many observations lie **above the median of the pooled sample**. Under $H_0$, roughly half of every group should.

## Statistic (lecture form)
- $\tilde Y$ = median of the pooled sample.
- $A_i=\#\{j:Y_{ij}>\tilde Y\}$ = number of observations in group $i$ above it.
- If $n$ is odd, the $A_i$ of the group that contains the median observation is increased by $\tfrac12$.

$$Q_M=4\sum_{i=1}^a\frac{A_i^2}{n_i}-n\;\overset{as.}{\sim}\;\chi^2(a-1);\qquad\text{reject if }Q_M\ge\chi^2_{1-\alpha}(a-1).$$

**What it really is:**
- It is Pearson's χ² for the $2\times a$ table (above / not above the median × group) → [[Contingency tables]].
- The formula above is that χ² simplified for the case where exactly half of all observations count as "above".

## Example: potatoes
- $\tilde Y=1.1$.
- Counts above 1.1: variety 1: 0/4, variety 2: 2/3, variety 3: 4/5, variety 4: 1/3 (7 of 15 overall).
- With ties at the median, the "exactly half" condition fails. R (agricolae) therefore computes the χ² of the $2\times4$ table:
  - $\chi^2=6.43$, $df=3$, $p=0.093$ → **not rejected**.
- Kruskal-Wallis on the same data rejects ($p=0.014$): the median test uses less information (only above/below), so it has **lower power**.

```r
with(dat, agricolae::Median.test(Weight, Variety))      # Chi Square = 6.4286, p = 0.0925
tab <- table(dat$Weight > median(dat$Weight), dat$Variety)
chisq.test(tab, correct = FALSE)                          # same 6.4286 (warns: small counts)
```

Related: [[Kruskal-Wallis test]] · [[Sign test]] (the same "above/below" idea for one sample)
