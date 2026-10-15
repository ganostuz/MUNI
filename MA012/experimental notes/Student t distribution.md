---
tags: [ma012, lecture/L01, type/distribution]
aliases: [t distribution, Student's t]
lecture: [L01, L05]
---

# Student t distribution $t(k)$

> [!info] Definition
> If $Z\sim N(0,1)$ and $V\sim\chi^2(k)$ are independent, then
> $$T=\frac{Z}{\sqrt{V/k}}\sim t(k).$$

**Shape:**
- symmetric around 0, with **heavier tails** than $N(0,1)$;
- $t(k)\to N(0,1)$ as $k\to\infty$.

## Origin: estimating $\sigma$ by $S$
$$T=\frac{\bar Y-\mu}{S_Y}\sqrt n=\frac{\overbrace{\tfrac{\bar Y-\mu}{\sigma}\sqrt n}^{Z\sim N(0,1)}}{\sqrt{\underbrace{\tfrac{(n-1)S_Y^2}{\sigma^2}}_{V\sim\chi^2(n-1)}/(n-1)}}\sim t(n-1)$$

Using the estimate $S_Y$ in place of the true $\sigma$ adds uncertainty, which shows up as the heavier tails.

## Where it appears
| Use | df | Note |
|---|---|---|
| one-sample t-test | $n-1$ | [[t-tests]] |
| two-sample pooled t-test | $n_X+n_Y-2$ | [[t-tests]] |
| Welch test | Welch–Satterthwaite (non-integer) | [[t-tests]] |
| CI for a group mean in ANOVA | $n-k$ | [[One-way ANOVA]] |
| test of Pearson correlation | $n-2$ | [[Pearson correlation]] |
| test of partial correlation | $n-m$ | [[Partial correlation]] |

```r
qt(0.975, df = 48.862)    # 2.0097 (critical value, Welch example in L01)
2 * pt(-abs(T), df)       # two-sided p-value
```

Related: [[Normal distribution]] · [[Chi-squared distribution]]
