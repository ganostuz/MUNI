---
tags: [ma012, lecture/L02, type/model, exam/1]
aliases: [Cell means model, Factor effects model, Reference group model, Treatment contrasts]
lecture: L02
exam: 1
---

# ANOVA parametrizations

The same model can be written in three ways. They have the same fitted values and the same F-test; only the **meaning of the parameters** differs.

| Model | Equation | Constraint | $H_0$ | Parameters mean |
|---|---|---|---|---|
| **cell means** | $Y_{ij}=\mu_i+\varepsilon_{ij}$ | none | $\mu_1=\dots=\mu_k$ | $\mu_i$ = group mean |
| **factor effects** | $Y_{ij}=\mu+\alpha_i+\varepsilon_{ij}$ | $\sum n_i\alpha_i=0$ | $\alpha_1=\dots=\alpha_k=0$ | $\mu$ = grand mean, $\alpha_i$ = deviation of group $i$ from it |
| **reference group** (treatment contrasts) | $Y_{ij}=\mu_1+\tau_i+\varepsilon_{ij}$ | $\tau_1=0$ | $\tau_2=\dots=\tau_k=0$ | $\mu_1$ = mean of the reference group (Intercept), $\tau_i=\mu_i-\mu_1$ |

**Link between the forms:** $\mu_i=\mu+\alpha_i=\mu_1+\tau_i$.

> [!warning] Slide 17
> It says "$\tau_i$ = the difference of $\mu_i$ from $\mu_i$". It should read "from $\mu_1$" → [[00 Slide errata]].

## Software defaults
- **R, Python, Stata:** the reference group is the **first** factor level.
- **SPSS, SAS:** the **last** level.
- Change it in R with `relevel(dat$Diet, ref = "C")`, or switch to sum-to-zero contrasts with `contrasts = list(Diet = contr.sum)`. That is needed for Type III SS → [[Types of sums of squares]].

## Diet example: the same fit in three languages
| | A | B | C |
|---|---|---|---|
| group means $\hat\mu_i$ (`model.tables(..., "means")`) | 3.300 | 3.026 | 5.148 |
| effects $\hat\alpha_i$ (`"effects"`), grand mean 3.845 | −0.545 | −0.819 | 1.303 |
| R coefficients | (Intercept) 3.300 | DietB −0.274 | DietC 1.848 |

**Check:** $\mu_1+\tau_C=3.300+1.848=5.148=\hat\mu_C$ ✔.

Related: [[One-way ANOVA]] · [[Two-way ANOVA]]
