---
tags: [ma012, lecture/L02, type/concept, exam/1]
aliases: [Submodel, Null model, Nested models, Model comparison]
lecture: L02
exam: 1
---

# Model and submodel

> [!info] Definitions
> - **One-way ANOVA model:** $M_A:\;Y_{ij}=\mu+\alpha_i+\varepsilon_{ij}$
> - **Null model:** $M_0:\;Y_{ij}=\mu+\varepsilon_{ij}$ (one common mean for everyone)
   
$M_0$ is a **submodel** of $M_A$: it arises from $M_A$ when all $\alpha_i=0$, which is exactly $H_0$.

**Hypothesis:** $H_0$: $M_0$ is valid; $H_1$: $M_A$ is valid.

## How the comparison works
- **Residual SS of the null model** = the total SS: $SS_e(M_0)=\sum(Y_{ij}-\bar Y_{\cdot\cdot})^2=SS_T$.
- **Residual SS of the ANOVA model:** $SS_e(M_A)=SS_e$.
- The bigger model always fits at least as well. The question is whether the improvement $SS_T-SS_e=SS_A$ is larger than chance would produce:
$$F=\frac{\big(SS_e(M_0)-SS_e(M_A)\big)/(d_e(M_0)-d_e(M_A))}{SS_e(M_A)/d_e(M_A)}=\frac{SS_A/(k-1)}{SS_e/(n-k)}=F_A$$
- So the omnibus F-test **is** the comparison of the model with its submodel.

## General principle
- The same F-test compares any pair of **nested linear models**.
- Used again in [[Two-way ANOVA]] (Type I/II/III sums of squares are sequences of submodel comparisons → [[Types of sums of squares]]).
- Also used in [[Linear regression model]] and [[Multicollinearity and stepwise regression]].

```r
m0 <- lm(WeightLoss ~ 1,    data = dat)
mA <- lm(WeightLoss ~ Diet, data = dat)
anova(m0, mA)      # same F = 6.197, p = 0.00323
```

Related: [[ANOVA sums of squares and F-test]] · [[One-way ANOVA]]
