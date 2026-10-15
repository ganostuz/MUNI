---
tags: [ma012, lecture/L01, type/concept, exam/13]
aliases: [Significance level, Power, Type I error, Type II error, alpha, beta]
lecture: L01
exam: 13
---

# Type I and II errors, significance level and power

| test decision | $H_1$ true | $H_0$ true |
|---|---|---|
| rejects $H_0$ | **true positive**, $P=1-\beta$ (= power) | **false positive** = Type I error, $P=\alpha$ |
| does not reject $H_0$ | **false negative** = Type II error, $P=\beta$ | **true negative**, $P=1-\alpha$ |

- **Significance level** $\alpha=P(\text{Type I error})$:
  - fixed **in advance**, independently of $n$ and $H_1$;
  - usually 0.05.
- **Power** $1-\beta$ = probability of detecting a real effect.
- No test can have $\alpha=0$ and $\beta=0$ at once. Tests are constructed so that $P(\text{Type I})\le\alpha$ and the power is as large as possible (uniformly most powerful tests).

**Power grows with:**
- the sample size $n$;
- the effect size (e.g. $|\mu_A-\mu_C|$);
- lower data variability;
- a larger $\alpha$.

**Trade-off:** lowering $\alpha$ from 0.05 to 0.01 moves the critical value outward, so power drops (slides 47–48).

```r
power.t.test(n = 25, delta = 2, sd = 2.4, sig.level = 0.05)   # power analysis
```

**Consequences elsewhere:**
- Running many tests inflates the overall Type I error → [[Multiple testing problem]].
- Conservative methods (Bonferroni, Scheffé) keep $\alpha$ but lose power → [[Post-hoc multiple comparisons]].
- Nonparametric tests are usually a little less powerful when the data really are normal → [[Parametric vs nonparametric methods]].

Related: [[Hypothesis testing]] · [[p-value]]
