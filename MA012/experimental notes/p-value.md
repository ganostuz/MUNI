---
tags: [ma012, lecture/L01, type/concept, exam/13]
aliases: [p value, P-value]
lecture: L01
exam: 13
---

# p-value

> [!info] Definition
> The **p-value** is the probability, **assuming $H_0$ is true**, that the test statistic takes a value **as extreme as or more extreme than** the observed $T^*$.
> Equivalently, it is the **smallest significance level $\alpha$ at which $H_0$ would be rejected**.

$$p=\begin{cases}2\min\{P(T\ge T^*),\,P(T\le T^*)\} & \text{two-sided}\\ P(T\ge T^*)\text{ or }P(T\le T^*) & \text{one-sided}\end{cases}$$

**Decision:** reject $H_0$ if $p\le\alpha$.

## What it is not
- **Not** $P(H_0\text{ is true})$.
- **Not** a measure of effect size or practical importance: a huge $n$ makes tiny effects "significant".
- Used **only** for the decision to reject or not.

## Computing it in R
```r
2 * pt(-abs(T), df)                       # two-sided t
pchisq(K, df, lower.tail = FALSE)         # chi-squared (right-tailed)
pf(F, d1, d2, lower.tail = FALSE)         # F (right-tailed)
2 * pnorm(-abs(U))                        # asymptotic normal
```

Examples:
- [[One-way ANOVA]]: $F=6.197$ on $(2,75)$ → $p=0.0032$.
- [[Wilcoxon signed-rank test]]: exact $p=0.037$.

Related: [[Hypothesis testing]] · [[Type I and II errors and power]] · [[Multiple testing problem]]
