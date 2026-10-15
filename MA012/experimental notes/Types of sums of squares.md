---
tags: [ma012, lecture/L02, type/concept]
aliases: [Type I SS, Type II SS, Type III SS, Sequential sums of squares, Unbalanced ANOVA]
lecture: L02
---

# Type I, II and III sums of squares (unbalanced two-way ANOVA)

**The problem with unequal cell sizes $n_{ij}$:**
- the design loses **orthogonality**;
- the main effects and the interaction become correlated;
- their SS overlap and no longer add up to $SS_T$.

Each "type" is a different sequence of [[Model and submodel|submodel comparisons]]:

| Type | Name | SS for $A$ | SS for $B$ | SS for $A{:}B$ |
|---|---|---|---|---|
| **I** | sequential | $M_0\to$ `Y~A` (**not adjusted for B!**) | `Y~A` → `Y~A+B` | `Y~A+B` → `Y~A+B+A:B` |
| **II** | hierarchical | `Y~B` → `Y~A+B` | `Y~A` → `Y~A+B` | `Y~A+B` → `Y~A+B+A:B` |
| **III** | partial | `Y~B+A:B` → full | `Y~A+A:B` → full | `Y~A+B` → full |

**Type I depends on the order in which the factors are entered.** Type II and III do not.

## Interpretation
1. **Interaction significant:**
   - Type I: don't interpret the main effects; look at simple effects.
   - Type II: the main-effect tests are biased (Type II assumes no interaction).
   - Type III: tests the main effects on **unweighted marginal means** while the interaction is in the model.
2. **Interaction not significant:**
   - Type I: $B$ is adjusted for $A$, but $A$ is **not** adjusted for $B$.
   - Types II and III: read $F_A$ and $F_B$ directly.

**In a balanced design** all three types coincide. That is why `ToothGrowth` gives identical tables.

```r
anova(lm(len ~ supp * dose, data = dat))                        # Type I
car::Anova(lm(len ~ supp * dose, data = dat), type = 2)         # Type II
car::Anova(lm(len ~ supp * dose, data = dat,
              contrasts = list(supp = contr.sum, dose = contr.sum)), type = 3)  # Type III
```

> [!warning] Type III needs sum-to-zero contrasts
> With R's default treatment contrasts, Type III tests are meaningless. See [[ANOVA parametrizations]].

Related: [[Two-way ANOVA]]
