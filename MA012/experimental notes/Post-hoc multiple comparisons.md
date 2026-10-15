---
tags: [ma012, lecture/L02, type/test, exam/1]
aliases: [Post-hoc tests, Multiple comparison, Tukey HSD, Bonferroni correction, Scheffé method, Linear contrast, Compact letter display, CLD]
lecture: [L02, L03]
exam: 1
---

# Post-hoc multiple comparisons

## When to use them
- After an omnibus test ([[One-way ANOVA]] F-test, [[Kruskal-Wallis test]]) **rejects** $H_0$, they answer *which* groups differ.
- If the omnibus test does **not** reject, stop: there is no evidence of any difference.
- They keep the **familywise** error rate at $\alpha$ → [[Multiple testing problem]].

## The methods
| Method | Idea | Strength | Use |
|---|---|---|---|
| **Bonferroni** (1936; Miller 1966) | each of the $s=\binom k2$ pairwise t-tests at $\alpha/s$ | very conservative, low power for many groups | $k\le4$ |
| **Tukey HSD** (1949) | studentized range distribution $q(k,n-k)$ | gold standard for **all pairs**; more powerful than Bonferroni | pairwise comparisons |
| **Scheffé** (1959) | $F$ quantiles; covers **all contrasts** | very conservative for pairs | complicated hypotheses, sets of groups |
| **linear contrasts** | test $L=\sum c_i\mu_i$ with $\sum c_i=0$ | targeted | planned comparisons |

### Tukey's simultaneous CI for $\mu_i-\mu_\ell$
$$(\bar Y_{i\cdot}-\bar Y_{\ell\cdot})\pm\frac{q_{1-\alpha}(k,\,n-k)}{\sqrt2}\sqrt{MS_e\left(\frac1{n_i}+\frac1{n_\ell}\right)}$$

**Decision:** the pair differs significantly ⇔ the CI excludes 0.

> [!example] Check by hand: A − C in the diet data
> 1. $q_{0.95}(3,75)=3.382$, so $q/\sqrt2=2.391$.
> 2. $\sqrt{5.736(1/24+1/27)}=0.672$.
> 3. Half-width $=1.607$, so the CI is $-1.848\pm1.607=(-3.455,\,-0.242)$.
> 4. The CI excludes 0 → A and C differ ✔ (matches R).

### Linear contrasts
- A contrast is $L=\sum_i c_i\mu_i$ with $\sum_i c_i=0$. Examples for $k=4$:
  - $\mu_1$ vs $\mu_4$: $c=(1,0,0,-1)$;
  - $\mu_1$ vs the average of $\mu_2$ and $\mu_4$: $c=(1,-\tfrac12,0,-\tfrac12)$.

## Diet results (p-values)
| Pair | Difference | Bonferroni | Tukey | Scheffé |
|---|---|---|---|---|
| A − B | 0.274 | 1.000 | 0.913 | 0.920 |
| A − C | −1.848 | 0.022 | 0.020 | 0.027 |
| B − C | −2.122 | 0.005 | 0.005 | 0.007 |

**Reading the table:**
- Tukey gives the smallest p-values (most powerful); Scheffé is the most conservative.
- Same conclusion with every method: **C differs from A and B; A ≈ B.**

## Compact letter display (CLD)
- Groups that **share a letter are not significantly different**.
- The original CLD ranks the groups by decreasing mean.
- Diets: C = *a*, A = *b*, B = *b*.

```r
TukeyHSD(anova_model)                                     # base R
agricolae::HSD.test(anova_model, "Diet", group = TRUE)$groups      # letters
agricolae::HSD.test(anova_model, "Diet", group = FALSE)$comparison # pairwise table
agricolae::LSD.test(anova_model, "Diet", p.adj = "bonferroni", group = TRUE)
agricolae::scheffe.test(anova_model, "Diet", group = TRUE)
pairwise.t.test(dat$WeightLoss, dat$Diet, p.adjust.method = "bonferroni")
qtukey(0.95, nmeans = 3, df = 75)                          # 3.38
```

**Nonparametric case:** after [[Kruskal-Wallis test]], use pairwise [[Mann-Whitney-Wilcoxon test]]s with the Bonferroni correction: `pairwise.wilcox.test(y, g, p.adjust.method = "bonferroni")`.

> [!check] Exam checklist
> - [ ] Why are post-hoc tests needed, and when are they performed?
> - [ ] The idea of Bonferroni, Tukey and Scheffé, and which is more conservative.
> - [ ] Read a CLD and a pairwise comparison table.
