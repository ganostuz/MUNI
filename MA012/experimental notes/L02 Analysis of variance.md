---
tags: [ma012, lecture/L02, moc]
aliases: [Lecture 2, ANOVA lecture]
lecture: L02
date: 2026-09-22
exam: 1
---

# L02 — Analysis of variance (ANOVA)

**Story of the lecture:**
1. Three diets, and pairwise t-tests would inflate the error rate → [[Multiple testing problem]].
2. Use **one** omnibus test instead: the one-way ANOVA F-test.
3. If it rejects, find *which* groups differ with post-hoc tests.
4. Check the assumptions.
5. Extend to two factors.

## One-way ANOVA (exam topic 1)
1. Model, hypotheses and the meaning of the parameters → [[One-way ANOVA]].
2. Three parametrizations (cell means, factor effects, reference group) → [[ANOVA parametrizations]].
3. $SS_T=SS_A+SS_e$, distributions, df, F statistic, ANOVA table → [[ANOVA sums of squares and F-test]].
4. Testing the null model against the ANOVA model → [[Model and submodel]].
5. Post-hoc tests: Bonferroni, Tukey HSD, Scheffé, contrasts, compact letter display → [[Post-hoc multiple comparisons]].
6. Assumptions and their checks: independence, normality (Shapiro-Wilk, Q-Q), equal variances (Levene, Bartlett); Welch ANOVA; Kruskal-Wallis → [[ANOVA assumptions and diagnostics]], [[Levene and Bartlett tests]].

**Running example:** weight loss (kg) after 6 weeks on diets A, B, C; $n=78$, $k=3$.

| Source | Df | Sum Sq | Mean Sq | F | p |
|---|---|---|---|---|---|
| Diet | 2 | 71.1 | 35.55 | 6.197 | 0.0032 |
| Residuals | 75 | 430.2 | 5.74 | | |

**Results:**
- The diet means differ.
- Tukey: C differs from both A and B; A and B do not differ (letters: C = *a*, A = *b*, B = *b*).
- Assumptions OK: Shapiro-Wilk on residuals $p=0.92$; Levene $p=0.54$.

## Two-way ANOVA
- Main effects, interaction, balanced design, the two-way table and testing order → [[Two-way ANOVA]].
- Unbalanced designs: Type I/II/III sums of squares → [[Types of sums of squares]].

**Running example:** `ToothGrowth` (guinea pig tooth length ~ supplement × dose). The interaction is significant ($p=0.022$).

> [!check] Exam checklist (topic 1)
> - [ ] Write the model equation, list the assumptions and interpret $\mu$, $\mu_i$, $\alpha_i$, $\varepsilon_{ij}$.
> - [ ] State $H_0$ three equivalent ways (means, effects, submodel).
> - [ ] Fill in an ANOVA table from partial information; give the df and distributions of the SS and $F$; decide.
> - [ ] Explain why post-hoc methods are needed and compare Bonferroni, Tukey and Scheffé.
