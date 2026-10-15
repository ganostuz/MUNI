---
tags: [ma012, lecture/L01, type/concept, exam/13]
aliases: [CI, Confidence intervals]
lecture: [L01, L02, L05]
exam: 13
---

# Confidence interval (CI)

> [!info] Definition
> A $100(1-\alpha)\%$ **confidence interval** for a parameter $\theta$ is a random interval computed from the sample that covers the true $\theta$ with probability $1-\alpha$.

**Interpretation of 95 %:** if the study were repeated many times, about 95 % of the intervals would contain the true $\theta$.

## Role in testing
- If $H_0:\theta=\theta_0$, then: **reject $H_0$ at level $\alpha$ ⇔ $\theta_0\notin CI_{1-\alpha}$**.
- A two-sided $H_1$ gives a two-sided CI.
- A one-sided $H_1$ gives a one-sided CI, e.g. $(-\infty,\,c)$ for $H_1:\theta<\theta_0$.
- **Advantage over the p-value:** the CI also shows the **size and precision** of the effect.

## CIs in this course
| Parameter | CI | Note |
|---|---|---|
| $\mu_A-\mu_C$ (Welch) | $(-3.15,\,-0.54)$, excludes 0 → reject | [[t-tests]] |
| group mean $\mu_i$ in ANOVA | $\bar Y_{i\cdot}\pm t_{1-\alpha/2}(n-k)\sqrt{MS_e/n_i}$ | [[One-way ANOVA]] |
| $\mu_i-\mu_\ell$ (Tukey) | $(\bar Y_{i\cdot}-\bar Y_{\ell\cdot})\pm\frac{q_{1-\alpha}(k,n-k)}{\sqrt2}\sqrt{MS_e(\frac1{n_i}+\frac1{n_\ell})}$ | [[Post-hoc multiple comparisons]] |
| $\rho$ (Pearson) | $\tanh\!\big(\operatorname{arctanh} r\pm u_{1-\alpha/2}/\sqrt{n-3}\big)$ | [[Fisher z-transformation]] |
| $p$ in the sign test | `binom.test` (Clopper–Pearson) | [[Sign test]] |

Related: [[Hypothesis testing]] · [[p-value]]
