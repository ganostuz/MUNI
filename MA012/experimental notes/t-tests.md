---
tags: [ma012, lecture/L01, type/test]
aliases: [t-test, Two-sample t-test, Welch test, One-sample t-test, Paired t-test, Student's t-test]
lecture: L01
---

# t-tests

All t-tests assume **normal data** (or large $n$, via the [[Central limit theorem]]) and independent observations.

## Overview
| Test | $H_0$ | Statistic | df |
|---|---|---|---|
| one-sample | $\mu=\mu_0$ | $T=\dfrac{\bar X-\mu_0}{S_X}\sqrt n$ | $n-1$ |
| paired | $\mu_{W-V}=0$ | one-sample test on $D_i=W_i-V_i$ | $n-1$ |
| two-sample, equal variances (pooled) | $\mu_X=\mu_Y$ | $T=\dfrac{\bar X-\bar Y}{S_p\sqrt{\frac1{n_X}+\frac1{n_Y}}}$ | $n_X+n_Y-2$ |
| two-sample, unequal variances (**Welch**) | $\mu_X=\mu_Y$ | $T=\dfrac{\bar X-\bar Y}{\sqrt{\frac{S_X^2}{n_X}+\frac{S_Y^2}{n_Y}}}$ | Welch–Satterthwaite $\nu$ |

**Pooled variance:**
$$S_p^2=\frac{(n_X-1)S_X^2+(n_Y-1)S_Y^2}{n_X+n_Y-2}$$

**Welch–Satterthwaite df:**
$$\nu=\frac{\left(\frac{S_X^2}{n_X}+\frac{S_Y^2}{n_Y}\right)^2}{\frac{(S_X^2/n_X)^2}{n_X-1}+\frac{(S_Y^2/n_Y)^2}{n_Y-1}}$$

> [!warning] Slide 38 of L01
> It writes $T\sim t(n-1)$ for the two-sample test. The correct df is $n_X+n_Y-2$ (pooled) or $\nu$ (Welch). See [[00 Slide errata]].

**Reject $H_0$** if $|T|\ge t_{1-\alpha/2}(df)$ (two-sided), or $T\le t_\alpha$ / $T\ge t_{1-\alpha}$ (one-sided).

## Worked example (L01): weight loss on diet A vs diet C
| | $n$ | mean | sd |
|---|---|---|---|
| A | 24 | 3.300 | 2.240 |
| C | 27 | 5.148 | 2.396 |

$$SE=\sqrt{\tfrac{5.018}{24}+\tfrac{5.739}{27}}=0.6493,\qquad T=\frac{3.300-5.148}{0.6493}=-2.846,\qquad \nu=48.86$$

```r
t.test(WeightLoss ~ Diet, data = data |> filter(Diet != "B"))   # Welch is R's default
# t = -2.8462, df = 48.862, p-value = 0.00645, 95% CI (-3.153, -0.543)
qt(c(0.025, 0.975), df = 48.862)                                 # ±2.0097
t.test(WeightLoss ~ Diet, data = ..., alternative = "less")      # p = 0.0032
t.test(..., var.equal = TRUE)                                    # pooled version
t.test(x, mu = 60)                                               # one-sample
t.test(after, before, paired = TRUE)                             # paired
```

**Decision:** the result is the same by all three rules → reject $H_0$; diet C gives larger mean weight loss.
- $p=0.0065<0.05$;
- $|T|=2.846>2.0097$;
- the CI excludes 0.

## Assumptions and alternatives
- **Normality:** check with [[Shapiro-Wilk test]] or a [[Q-Q plot]] per group. If violated with a small $n$ → [[Mann-Whitney-Wilcoxon test]] (two samples) or [[Wilcoxon signed-rank test]] / [[Sign test]] (one sample or paired).
- **Equal variances:** check with [[Levene and Bartlett tests]]. When in doubt, use Welch.
- **More than two groups:** don't run many t-tests → [[Multiple testing problem]] → [[One-way ANOVA]].

Related: [[Student t distribution]] · [[Paired vs two-sample design]] · [[Hypothesis testing]]
