---
tags: [ma012, lecture/L04, type/concept]
aliases: [Skewness, Kurtosis, Excess kurtosis, Cullen-Frey plot, Šikmost, Špičatost]
lecture: L04
---

# Skewness and kurtosis (and the Cullen–Frey plot)

## Sample formulas
$$b_1=\frac{\frac1n\sum(X_i-\bar X)^3}{\left[\frac1n\sum(X_i-\bar X)^2\right]^{3/2}},\qquad b_2^*=\frac{\frac1n\sum(X_i-\bar X)^4}{\left[\frac1n\sum(X_i-\bar X)^2\right]^2},\qquad b_2=b_2^*-3$$

**Skewness $b_1$** measures asymmetry:
| $b_1$ | Meaning | Order of the centres |
|---|---|---|
| $>0$ | long **right** tail | mode < median < mean |
| $<0$ | long **left** tail | mean < median < mode |
| $=0$ | symmetric | mean = median = mode (if unimodal) |

**Kurtosis $b_2^*$** measures **tailedness**, i.e. the weight of the tails (not really the peak):
- normal: $b_2^*=3$, so the **excess kurtosis** $b_2=0$;
- $b_2>0$: heavier tails and more outliers (e.g. $t$, lognormal);
- $b_2<0$: lighter tails (e.g. uniform).

## For normal data (asymptotically)
$$b_1\sim N\!\left(0,\tfrac{6(n-2)}{(n+1)(n+3)}\right),\qquad b_2\sim N\!\left(-\tfrac6{n+1},\tfrac{24n(n-2)(n-3)}{(n+1)^2(n+3)(n+5)}\right)$$
- Roughly, $\mathrm{Var}\,b_1\approx6/n$ and $\mathrm{Var}\,b_2\approx24/n$.
- These results lead to the D'Agostino, Anscombe–Glynn and Jarque–Bera tests → [[Normality tests]].

> [!warning] Slide 25 of L04
> The variance of $b_2$ is missing the factor $n$ → [[00 Slide errata]].

## Cullen–Frey plot
`fitdistrplus::descdist(x, boot = 1000)`
- **Axes:** x = **squared skewness**, y = **kurtosis** (non-excess; normal = 3).
- **Points** (distributions with a fixed shape):

| distribution | skewness² | kurtosis |
|---|---|---|
| normal | 0 | 3 |
| uniform | 0 | 1.8 |
| logistic | 0 | 4.2 |
| exponential | 4 | 9 |

- **Lines:** gamma (kurtosis $=3+1.5\,\text{skew}^2$) and lognormal. Weibull lies close to them.
- **Area:** beta.
- **Data:** the blue dot is your sample; the orange bootstrap cloud shows its uncertainty.
- **Use:** shortlist candidate distributions, then test them formally.

```r
moments::skewness(x); moments::kurtosis(x)    # kurtosis() INCLUDES the 3
fitdistrplus::descdist(x, boot = 1000)
```

**Example (average restart time):** skewness 1.67 and kurtosis 8.40 (excess 5.4) → right-skewed with a heavy tail.

Related: [[Normal distribution]] · [[Descriptive statistics and EDA]] · [[Q-Q plot]]
