---
tags: [ma012, lecture/L02, type/test, exam/1]
aliases: [Levene's test, Bartlett's test, Homogeneity of variance test, Homoscedasticity]
lecture: L02
exam: 1
---

# Levene's and Bartlett's tests (homogeneity of variance)

**Hypotheses:** $H_0:\sigma_1^2=\dots=\sigma_k^2$ vs $H_1$: at least two variances differ.

| | **Levene** | **Bartlett** |
|---|---|---|
| Idea | one-way ANOVA on the absolute deviations $Z_{ij}=\lvert Y_{ij}-\tilde Y_{i}\rvert$ (centre = median by default: Brown–Forsythe variant) | likelihood-ratio comparison of $\ln S_p^2$ with the $\ln S_i^2$ |
| Distribution | $F(k-1,\,n-k)$ | $\chi^2(k-1)$ |
| Normality needed? | no; robust to outliers | **yes**, exactly; not robust to outliers |
| Power | lower | higher (when the data are normal) |
| R | `car::leveneTest(y ~ A)` | `bartlett.test(y ~ A)` |

**Bartlett statistic** (for reference):
$$B=\frac{(n-k)\ln S_p^2-\sum_i(n_i-1)\ln S_i^2}{1+\frac1{3(k-1)}\left(\sum_i\frac1{n_i-1}-\frac1{n-k}\right)},\qquad S_p^2=MS_e$$

**Recommendation:** Levene is the standard choice; use Bartlett only when normality clearly holds.

**Diets:** Levene $F=0.626$, $p=0.538$ → variances are homogeneous.
**ToothGrowth (6 cells):** Levene $F=1.709$ on $(5,54)$, $p=0.148$.

Related: [[ANOVA assumptions and diagnostics]] · [[F distribution]]
