---
tags: [ma012, lecture/L03, type/test, exam/2]
aliases: [Mann-Whitney U test, Wilcoxon rank-sum test, MWW test, Two-sample Wilcoxon test, Dvouvýběrový Wilcoxonův test]
lecture: L03
exam: 2
---

# Mann–Whitney–Wilcoxon (rank-sum) test

**History:** Wilcoxon (1945); Mann & Whitney (1947).
**Use:** compare **two independent samples** of at least ordinal data, $(X_1..X_m)\sim F_X$ and $(Y_1..Y_n)\sim F_Y$.

## Hypotheses (three versions)
| Version | $H_0$ | $H_1$ |
|---|---|---|
| Mann–Whitney (continuous data) | $P(X>Y)=P(X<Y)$ | one variable is stochastically larger |
| general | $F_X=F_Y$ | $F_X(x)\ne F_Y(x)$ for some $x$ (consistent only if $P(X>Y)\ne\tfrac12$) |
| **location shift** (stricter assumptions) | $F_X=F_Y$ | $F_X(x)=F_Y(x-\delta)$, $\delta\ne0$ → a test of **different medians** |

## Algorithm
1. Pool the two samples ($m+n$ values) and sort them.
2. Rank in the pooled sample: $R_1..R_m$ for the $X$'s and $S_1..S_n$ for the $Y$'s.
3. Rank sums: $T_1=\sum R_i$ and $T_2=\sum S_j$.
4. U statistics:
$$U_1=T_1-\frac{m(m+1)}2,\qquad U_2=T_2-\frac{n(n+1)}2,\qquad U_{MW}=\min\{U_1,U_2\}$$

## Properties
- $U_1=\#\{(X_i,Y_j):X_i>Y_j\}$, counting over all $mn$ pairs. This is its intuitive meaning.
- $U_1+U_2=mn$.
- Under $H_0$: $E\,U=\frac{mn}2$ and $\mathrm{Var}\,U=\frac{mn(m+n+1)}{12}$. The exact distribution is `dwilcox(x, m, n)`.

## Decision
- **Exact:** reject if $U_{MW}\le w_\alpha(m,n)$.
- **Asymptotic** (good for $m,n\ge20$):
$$U=\frac{U_{MW}-mn/2}{\sqrt{mn(m+n+1)/12}}\overset{as.}{\sim}N(0,1);\qquad\text{reject if }|U|\ge u_{1-\alpha/2}.$$

## Example: wheat yield, fertilizer A ($m=8$) vs B ($n=5$)
- A: 5.7, 5.5, 4.3, 5.9, 5.2, 5.6, 5.8, 5.1
- B: 5.0, 4.5, 4.2, 5.4, 4.4

**Pooled ranks:**
| value | 4.2 | 4.3 | 4.4 | 4.5 | 5.0 | 5.1 | 5.2 | 5.4 | 5.5 | 5.6 | 5.7 | 5.8 | 5.9 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| group | B | A | B | B | B | A | A | B | A | A | A | A | A |
| rank | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 |

1. $T_1=2+6+7+9+10+11+12+13=70$ and $T_2=1+3+4+5+8=21$ (check: $91=13\cdot14/2$ ✔).
2. $U_1=70-36=34$, $U_2=21-15=6$ (check: $34+6=40=mn$ ✔), so $U_{MW}=6$.
3. $EU=20$, $\mathrm{Var}\,U=8\cdot5\cdot14/12=140/3$, so $U=(6-20)/6.83=-2.05$, and $|U|>1.96$ → **reject $H_0$**.
4. **Exact:** $P(U\le6)=0.0225\le0.025$, so $w_{0.05}(8,5)=6$ and $U_{MW}=6\le6$ → reject. Exact $p=0.045$.

```r
dat <- data.frame(Yield = c(5.7,5.5,4.3,5.9,5.2,5.6,5.8,5.1, 5.0,4.5,4.2,5.4,4.4),
                  Fertilizer = factor(rep(c("A","B"), c(8,5))))
wilcox.test(Yield ~ Fertilizer, data = dat)    # W = 34 (= U1 for the first level), p = 0.04507
pwilcox(6, 8, 5)                               # 0.0225
```

> [!tip] R's W
> R reports $W=U_1$ for the **first** factor level, not $\min(U_1,U_2)$. The p-value is the same.

## Notes
- **Terminology:** Mann–Whitney U = Mann–Whitney–Wilcoxon = Wilcoxon rank-sum = two-sample Wilcoxon test.
- **Ties:** use average ranks; R then uses the variance adjusted for ties.
- **Prefer it to the t-test when:**
  - the data are ordinal, or
  - there are outliers.
- **Weaknesses:**
  - If the shapes differ (e.g. different variances), $H_0$ can be rejected even with equal medians.
  - The Type I error can be off when the data are both heteroscedastic and non-normal.
- **Extension to $\ge3$ groups:** [[Kruskal-Wallis test]].

Related: [[t-tests]] · [[Order statistics and ranks]] · [[Permutation tests]]
