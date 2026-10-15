---
tags: [ma012, lecture/L02, type/concept, exam/1]
aliases: [ANOVA assumptions, Welch ANOVA, Residuals in ANOVA]
lecture: L02
exam: 1
---

# ANOVA assumptions and diagnostics

| Assumption | How to check | If violated |
|---|---|---|
| **independence** of $Y_{ij}$ | design of the experiment (no test) | different model (repeated measures, mixed models) |
| **normality** $\varepsilon_{ij}\sim N(0,\sigma^2)$ | [[Shapiro-Wilk test]] on the residuals (pooled; then per group if suspicious); [[Q-Q plot]] | large $n_i$: ANOVA is robust; small $n_i$ with strong skewness or outliers → [[Kruskal-Wallis test]] |
| **homogeneity of variance** $\mathrm{Var}\,Y_{ij}=\sigma^2$ | [[Levene and Bartlett tests]]; boxplots; residuals vs fitted plot | transform the data, or **Welch ANOVA** |

## Procedure
1. Fit the model.
2. Compute the **residuals** $r_{ij}=Y_{ij}-\bar Y_{i\cdot}$.
3. Check the normality of the residuals (all together; per group if suspicious).
4. Check the homogeneity of the variances.

## How robust is ANOVA?
**Normality:**
- The F-test really needs normal **group means**, not normal observations.
- With $n_i\ge15$, the [[Central limit theorem]] makes it robust to mild skewness and kurtosis.
- With $n_i<15$, strong skewness and outliers bias the means and inflate $MS_e$.
- Lilliefors needs $n\ge100$ and has weak power; Anderson–Darling suits $n\ge50$ → [[Normality tests]].

**Variances:**
- **Balanced design** ($n_1=\dots=n_k$): ANOVA tolerates a variance ratio up to about 1:4 (an SD or IQR ratio up to 1:2).
- **Unbalanced design:** p-values can be too small (liberal) or too large (conservative, low power).
- **Welch ANOVA** (1951) does not assume equal variances: `oneway.test(y ~ A, var.equal = FALSE)`.

## Diet example
```r
diag <- broom::augment(anova_model)          # adds .resid, .fitted
shapiro.test(diag$.resid)                    # W = 0.992, p = 0.916  -> normal
diag |> summarise(.by = Diet, p = shapiro.test(.resid)$p.value)   # B 0.961, A 0.077, C 0.372
car::leveneTest(WeightLoss ~ Diet, data = dat)   # F = 0.626, p = 0.538 -> equal variances
bartlett.test(WeightLoss ~ Diet, data = dat)
oneway.test(WeightLoss ~ Diet, data = dat, var.equal = FALSE)   # Welch ANOVA
```

**Conclusion:** all p-values > 0.05, so there is no evidence against the assumptions and the F-test is valid.

**Plots to show:**
- a Q-Q plot of the residuals (pooled and per group);
- boxplots of the residuals by group;
- residuals vs fitted values (group means). The spread should look similar in all groups.

Related: [[One-way ANOVA]] · [[Two-way ANOVA]] · [[Regression diagnostics]]
