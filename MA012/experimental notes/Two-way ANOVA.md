---
tags: [ma012, lecture/L02, type/model, type/test]
aliases: [Two-way analysis of variance, Interaction effect, Main effect, Dvoufaktorová analýza rozptylu]
lecture: L02
---

# Two-way ANOVA

**Setting:**
- one numeric response, two nominal factors: $A$ with $a$ levels and $B$ with $b$ levels;
- $Y_{ijk}$ = the $k$-th observation in cell $(i,j)$;
- **balanced design:** all $n_{ij}=m$, so $n=abm$.

## Model
> [!important] Model
> $$Y_{ijk}=\underbrace{\mu+\alpha_i+\beta_j+\lambda_{ij}}_{\mu_{ij}}+\varepsilon_{ijk},\qquad\varepsilon_{ijk}\sim\text{i.i.d. }N(0,\sigma^2)$$
> Identification constraints: $\sum_i\alpha_i=0$, $\sum_j\beta_j=0$, $\sum_i\lambda_{ij}=0$ for every $j$, $\sum_j\lambda_{ij}=0$ for every $i$.

| Parameter | Meaning |
|---|---|
| $\mu$ | grand mean |
| $\mu_{ij}$ | cell mean |
| $\alpha_i$ | **main effect** of level $i$ of $A$ (averaged over $B$) |
| $\beta_j$ | main effect of level $j$ of $B$ |
| $\lambda_{ij}$ | **interaction effect**: the extra shift in cell $(i,j)$ beyond $\alpha_i+\beta_j$ |

The assumptions are the same as in [[One-way ANOVA]] (independence, normality, equal variance in all $a\times b$ cells).

## Sums of squares (balanced design, Fisher 1925)
| SS | Formula | df |
|---|---|---|
| $SS_A$ | $bm\sum_i(\bar Y_{i\cdot\cdot}-\bar Y_{\cdot\cdot\cdot})^2$ | $a-1$ |
| $SS_B$ | $am\sum_j(\bar Y_{\cdot j\cdot}-\bar Y_{\cdot\cdot\cdot})^2$ | $b-1$ |
| $SS_{A:B}$ | $m\sum_i\sum_j(\bar Y_{ij\cdot}-\bar Y_{i\cdot\cdot}-\bar Y_{\cdot j\cdot}+\bar Y_{\cdot\cdot\cdot})^2$ | $(a-1)(b-1)$ |
| $SS_e$ | $\sum_{ijk}(Y_{ijk}-\bar Y_{ij\cdot})^2$ | $n-ab$ |
| $SS_T$ | $\sum_{ijk}(Y_{ijk}-\bar Y_{\cdot\cdot\cdot})^2$ | $n-1$ |

**Theorem (balanced design):** $SS_T=SS_A+SS_B+SS_{A:B}+SS_e$.

Each $F_{\text{effect}}=MS_{\text{effect}}/MS_e$ follows $F(d_{\text{effect}},\,n-ab)$ under its own $H_0$.

## Testing order
1. **Test the interaction first:** $H_0$: all $\lambda_{ij}=0$.
2. **If it is significant:**
   - the effect of $A$ depends on the level of $B$, and main effects can mislead;
   - examine **simple main effects** (the effect of $A$ within each level of $B$) or compare the cells.
3. **If it is not significant:** test and interpret the main effects:
   - $H_0:\mu_{1\cdot}=\dots=\mu_{a\cdot}$ (all $\alpha_i=0$);
   - $H_0:\mu_{\cdot1}=\dots=\mu_{\cdot b}$ (all $\beta_j=0$).

## Example: `ToothGrowth` (tooth length ~ supplement OJ/VC × dose 0.5/1/2 mg; $m=10$, $n=60$)
```r
dat <- transform(ToothGrowth, dose = factor(dose))
anova_model <- aov(len ~ supp * dose, data = dat)   # = supp + dose + supp:dose
summary(anova_model)
```

| Source | Df | Sum Sq | Mean Sq | F | p |
|---|---|---|---|---|---|
| supp | 1 | 205.4 | 205.4 | 15.57 | 0.0002 |
| dose | 2 | 2426.4 | 1213.2 | 92.00 | <2e-16 |
| supp:dose | 2 | 108.3 | 54.2 | 4.11 | 0.022 |
| Residuals | 54 | 712.1 | 13.2 | | |

**Interpretation:**
- The **interaction is significant** ($p=0.022$): the advantage of orange juice (OJ) over ascorbic acid (VC) depends on the dose.
- Cell means:
  - dose 0.5 and 1: OJ is clearly better (13.2 vs 8.0; 22.7 vs 16.8);
  - dose 2: no difference (26.1 vs 26.1).
- So comparing the 6 cells (Tukey letters) is more informative than the main-effect tests.

**Estimated effects** (`model.tables(anova_model, "effects")`):
- supp: OJ +1.85, VC −1.85;
- dose: −8.21, +0.92, +7.29;
- interaction for OJ: +0.775, +1.115, −1.890.

**Check one interaction effect by hand:** $\bar Y_{OJ,0.5}-\bar Y_{OJ}-\bar Y_{0.5}+\bar Y=13.23-20.663-10.605+18.813=0.775$ ✔.

**R coefficients** (reference-group form; reference cell = OJ, 0.5):
- Intercept 13.23;
- suppVC −5.25;
- dose1 9.47, dose2 12.83;
- suppVC:dose1 −0.68, suppVC:dose2 5.33.

**Post-hoc test on the cells:**
```r
agricolae::HSD.test(anova_model, c("supp", "dose"), group = TRUE)$groups
#  VC:2 a, OJ:2 a, OJ:1 a, VC:1 b, OJ:0.5 b, VC:0.5 c
```

**Assumptions:**
- Shapiro-Wilk on the residuals: $p=0.67$;
- per-cell Shapiro-Wilk p-values: 0.17–0.92;
- Levene: $p=0.148$.

## Unbalanced designs
When the $n_{ij}$ differ, the SS no longer add up to $SS_T$, and the order of the factors matters → [[Types of sums of squares]].

Related: [[One-way ANOVA]] · [[Post-hoc multiple comparisons]] · [[Model and submodel]]
