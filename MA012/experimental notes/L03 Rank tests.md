---
tags: [ma012, lecture/L03, moc]
aliases: [Lecture 3, Rank-based tests lecture]
lecture: L03
date: 2026-09-29
exam: 2
---

# L03 — Ranks, rank-based tests, permutation tests

**Story of the lecture:**
1. When we can't assume normality, work with **ranks**.
2. Their distribution under $H_0$ doesn't depend on the data's distribution.
3. This gives a nonparametric twin for each classic test.
4. Finally, permutation tests build the null distribution by rearranging the data.

## Building blocks
- [[Parametric vs nonparametric methods]]
- [[Order statistics and ranks]]: ordered sample, order statistics, ranks, ties, ranking methods.
- [[Paired vs two-sample design]]

## Tests
| Test | Samples | $H_0$ (location) | Parametric twin | Note |
|---|---|---|---|---|
| sign test | 1 or paired | median $=x_0$ | one-sample t-test | [[Sign test]] |
| Wilcoxon signed-rank | 1 or paired, **symmetric** | centre of symmetry $=x_0$ | one-sample t-test | [[Wilcoxon signed-rank test]] |
| Mann-Whitney-Wilcoxon (rank-sum) | 2 independent | same distribution | two-sample t-test | [[Mann-Whitney-Wilcoxon test]] |
| Kruskal-Wallis | $a\ge2$ independent | same distribution | one-way ANOVA | [[Kruskal-Wallis test]] |
| median test | $a\ge2$ independent | same median | — | [[Median test]] |
| Van der Waerden | $a\ge2$ independent | as Kruskal-Wallis, using normal scores | — | [[Van der Waerden test]] |
| permutation test | any | exchangeability | — | [[Permutation tests]] |

## Running examples (all recomputed ✔)
- **One minute guessed by 10 people**, $H_0:\tilde x=60$:
  - sign test $S^+=2$, $p=0.109$ → not rejected;
  - Wilcoxon $T^+=7$, $p=0.037$ → rejected.
- **Wheat yield, fertilizer A (8 fields) vs B (5 fields):** $U_1=34$, $U_2=6$, $p=0.045$ → rejected.
- **Potato weight across 4 varieties:**
  - Kruskal-Wallis $Q_{adj}=10.68$, $p=0.014$ → rejected;
  - median test $p=0.093$ → not rejected.

> [!check] Exam checklist (topic 2)
> - [ ] Compare parametric and nonparametric methods.
> - [ ] Define a random sample, the ordered sample, order statistics and ranks; rank data with ties (average ranks).
> - [ ] Sign test, Wilcoxon signed-rank and rank-sum tests: assumptions, hypotheses, how to compute the statistic\*, decision.
> - [ ] Kruskal-Wallis: model, hypothesis, comparison with one-way ANOVA.
