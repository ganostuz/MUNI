---
tags: [ma012, lecture/L02, type/test, type/model, exam/1]
aliases: [ANOVA, One-way analysis of variance, Jednofaktorová analýza rozptylu]
lecture: L02
exam: 1
---

# One-way ANOVA

## Purpose
- Compare the **expected values of a numeric response $Y$** across $k\ge2$ independent groups, given by the levels of a **nominal factor $A$**.
- It is the generalization of the two-sample [[t-tests|t-test]].
- It needs **one test only**, so the familywise error rate stays at $\alpha$ → [[Multiple testing problem]].
- History: Fisher, 1918–1925.

## Data and notation
| Symbol                                           | Meaning                                               |
| ------------------------------------------------ | ----------------------------------------------------- |
| $Y_{ij}$                                         | $j$-th observation in group $i$, $i=1..k$, $j=1..n_i$ |
| $n=\sum n_i$                                     | total sample size                                     |
| $\bar Y_{i\cdot}=\frac1{n_i}\sum_j Y_{ij}$       | group sample mean                                     |
| $\bar Y_{\cdot\cdot}=\frac1n\sum_i\sum_j Y_{ij}$ | overall sample mean                                   |

Tidy data: one numeric column $Y$ and one `factor` column $A$ → [[Tidy data and tidyverse]].

## Model (factor effects form)
> [!important] Model
> $$Y_{ij}=\underbrace{\mu+\alpha_i}_{\mu_i}+\varepsilon_{ij},\qquad \varepsilon_{ij}\sim\text{i.i.d. }N(0,\sigma^2),\qquad \sum_{i=1}^k n_i\alpha_i=0$$

| Parameter | Meaning | Unbiased estimator |
|---|---|---|
| $\mu$ | grand mean | $\bar Y_{\cdot\cdot}$ |
| $\mu_i$ | group mean of group $i$ | $\bar Y_{i\cdot}$ |
| $\alpha_i=\mu_i-\mu$ | **effect** of group $i$ (its deviation from the grand mean) | $\bar Y_{i\cdot}-\bar Y_{\cdot\cdot}$ |
| $\varepsilon_{ij}$ | random error | residual $r_{ij}=Y_{ij}-\bar Y_{i\cdot}$ |
| $\sigma^2$ | error variance | $MS_e$ |

**Why the constraint $\sum n_i\alpha_i=0$?** There are $k+1$ parameters ($\mu,\alpha_1..\alpha_k$) but only $k$ group means. Without one constraint the parameters would not be identifiable. With this weighting, $\hat\mu$ is exactly the overall mean.

Other parametrizations (cell means, reference group as used by R) → [[ANOVA parametrizations]].

## Assumptions
1. The observations $Y_{ij}$ are **mutually independent** (ensured by the design).
2. The errors are **normal**: $\varepsilon_{ij}\sim N(0,\sigma^2)$.
3. **Homogeneous variance**: the same $\sigma^2$ in all groups.

How to check them → [[ANOVA assumptions and diagnostics]].

## Hypotheses
Three equivalent forms:

| Form    | $H_0$                                                     | $H_1$                                                    |
| ------- | --------------------------------------------------------- | -------------------------------------------------------- |
| means   | $\mu_1=\dots=\mu_k$                                       | $\exists\,i,\ell:\mu_i\ne\mu_\ell$ (at least two differ) |
| effects | $\alpha_1=\dots=\alpha_k=0$                               | $\exists\,i:\alpha_i\ne0$                                |
| models  | the null model $M_0:Y_{ij}=\mu+\varepsilon_{ij}$ is valid | the ANOVA model $M_A$ is valid                           |

The third form is explained in [[Model and submodel]].

> [!warning] Slide 6
> It writes $H_0:\mu_1=\dots=\mu_k=0$. The "$=0$" is a typo: the means only need to be **equal** → [[00 Slide errata]].

## Test
Partition the variability ($SS_T=SS_A+SS_e$) and compute $F_A=MS_A/MS_e\sim F(k-1,n-k)$ under $H_0$ → [[ANOVA sums of squares and F-test]].

## Example (diets)
```r
anova_model <- aov(WeightLoss ~ Diet, data = dat)
summary(anova_model)                       # F = 6.197, p = 0.00323
model.tables(anova_model, type = "means")  # grand 3.845; A 3.300, B 3.026, C 5.148
model.tables(anova_model, type = "effects")# A -0.545, B -0.819, C 1.303
coefficients(anova_model)                  # (Intercept) 3.300, DietB -0.274, DietC 1.848
```

**Check the effects by hand:** $\hat\alpha_A=3.300-3.845=-0.545$, and $\sum n_i\hat\alpha_i=24(-0.545)+27(-0.819)+27(1.303)\approx0$ ✔.

**CI for a group mean:**
$$\bar Y_{i\cdot}\pm t_{1-\alpha/2}(n-k)\sqrt{MS_e/n_i}$$
For diet C: $5.148\pm1.992\sqrt{5.736/27}=5.148\pm0.918=(4.23,\,6.07)$.

**If $H_0$ is rejected →** [[Post-hoc multiple comparisons]].
**If the assumptions fail →** Welch ANOVA, or the [[Kruskal-Wallis test]].

> [!check] Exam checklist
> - [ ] Model equation and the meaning of each symbol.
> - [ ] Relation between effects and means: $\mu_i=\mu+\alpha_i$, $\sum n_i\alpha_i=0$.
> - [ ] Three equivalent formulations of $H_0$.
> - [ ] Assumptions and how to test them.

Related: [[L02 Analysis of variance]] · [[Two-way ANOVA]] · [[Linear regression model]] (ANOVA is a linear model with a factor)
