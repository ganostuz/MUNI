---
tags: [ma012, lecture/L05, type/test, exam/5]
aliases: [Pearson correlation coefficient, Correlation coefficient, Covariance, Sample covariance, Product-moment correlation, Test of uncorrelation]
lecture: L05
exam: 5
---

# Covariance and Pearson correlation

## Population vs sample
| Population (probability) | Sample (statistics) |
|---|---|
| $\mathrm{Cov}(X,Y)=E\big([X-EX][Y-EY]\big)$ | $S_{XY}=\frac1{n-1}\sum(X_i-\bar X)(Y_i-\bar Y)$ |
| $\rho_{XY}=\dfrac{\mathrm{Cov}(X,Y)}{\sqrt{\mathrm{Var}X}\sqrt{\mathrm{Var}Y}}$ | $r_{XY}=\dfrac{S_{XY}}{S_XS_Y}$ |

- History: Galton (1880s), Bravais (1844), Karl Pearson (1895; the slide's "1985" is a typo).
- Also called the **product-moment** correlation coefficient.

## What $r$ measures
**The strength and direction of LINEAR association.**
- $-1\le r\le1$, and $r$ is symmetric in $X,Y$.
- $r>0$: as $X$ grows, $Y$ grows. $r<0$: as $X$ grows, $Y$ falls.
- $\lvert r\rvert=1$: the points lie exactly on a line.
- **Recommended for** interval/ratio data that are bivariate normal → [[Bivariate normal distribution]].
- $r$ is a **biased** estimator of $\rho$.

| $\lvert r\rvert$ | interpretation |
|---|---|
| 0.0–0.2 | very weak / none |
| 0.2–0.4 | weak |
| 0.4–0.6 | moderate |
| 0.6–0.8 | strong |
| 0.8–1.0 | very strong |

## Test of (un)correlation
- **Hypotheses:** $H_0:\rho_{XY}=0$ (uncorrelated) vs $H_1:\rho_{XY}\ne0$ (correlated).
- **Assumption:** a bivariate normal sample with $n\ge3$.

$$T=r\sqrt{\frac{n-2}{1-r^2}}\sim t(n-2)\text{ under }H_0;\qquad\text{reject if }|T|\ge t_{1-\alpha/2}(n-2),\quad p=2[1-F_t(|T|)].$$

**Caveats:**
- If the data are not normal, the $t$ distribution is only approximate.
- As $r\to\pm1$, the distribution of $r$ becomes very skewed. For tests of $\rho=\rho_0\ne0$ and for CIs, use the [[Fisher z-transformation]].

## Example: Members vs Expense ($n=7$)
1. $r=0.8896$.
2. $T=0.8896\sqrt{5/(1-0.7914)}=4.356>t_{0.975}(5)=2.571$ → **reject $H_0$**, $p=0.0073$.
3. 95 % CI for $\rho$: $(0.414,\,0.984)$.
4. **Conclusion:** expenses and household size are very strongly positively correlated.

```r
cor(dat$Members, dat$Expense)        # 0.8896
cor.test(dat$Members, dat$Expense)   # t = 4.3557, df = 5, p = 0.00732, CI (0.414, 0.984)
cov(dat$Members, dat$Expense)        # 19.64
```

> [!warning] Interpretation limits
> - $r$ only captures **linear** association.
> - It is sensitive to outliers.
> - Correlation ≠ causation, and **uncorrelated ≠ independent** → [[Correlation pitfalls]].
> - Always plot the data.

Related: [[Spearman correlation]] · [[Kendall tau]] · [[Covariance and correlation matrix]] · [[Partial correlation]] · [[Linear regression model]]
