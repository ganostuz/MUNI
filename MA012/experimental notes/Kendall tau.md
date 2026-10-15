---
tags: [ma012, lecture/L05, type/test, exam/5]
aliases: [Kendall's tau, Kendall rank correlation, Concordant and discordant pairs, Concordant pairs, Discordant pairs]
lecture: L05
exam: 5
---

# Kendall's tau

## Concordant and discordant pairs
Take two observations $(X_i,Y_i)$ and $(X_j,Y_j)$, $i<j$. The pair is:
- **concordant** if $(X_i-X_j)(Y_i-Y_j)>0$: both variables move in the same direction;
- **discordant** if $(X_i-X_j)(Y_i-Y_j)<0$: they move in opposite directions;
- **neither** if there is a tie in $X$ or in $Y$.

## Definition
$$\tau_{XY}=\frac{n_+-n_-}{\sqrt{n_0-n_X}\sqrt{n_0-n_Y}}$$

| Symbol | Meaning |
|---|---|
| $n_0=\binom n2=\frac{n(n-1)}2$ | number of all pairs |
| $n_+$ | number of concordant pairs |
| $n_-$ | number of discordant pairs |
| $n_X=\frac12\sum_iu_i(u_i-1)$ | tie correction for $X$ ($u_i$ = size of each tie group); 0 without ties |
| $n_Y=\frac12\sum_jv_j(v_j-1)$ | the same for $Y$ |

**Without ties:** $\tau=\frac{n_+-n_-}{n_0}$.

## Meaning
- $-1\le\tau\le1$; it measures **ordinal association** and looks only at the ordering, not at distances.
- **Typical use:** ordinal data, and especially **comparing two rankings** (e.g. two judges).
- $n_-$ = the number of swaps of neighbours (inversions) needed to turn the $X$-ranking into the $Y$-ranking.
- Usually $|\tau|<|r^{Sp}|$ for the same data.

## Test
**Hypotheses:** $H_0:\tau=0$; $H_1:\tau\ne0$ (ordinally associated).

**Asymptotic, no ties:** reject if
$$\sqrt{\frac{9n(n-1)}{2(2n+5)}}\,|\tau|\ge u_{1-\alpha/2}.$$
With small samples or ties, an adjusted formula is used (R does this automatically).

## Example 1: Members ($M$) vs Expense ($E$), $n=7$
| $i$ | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
|---|---|---|---|---|---|---|---|
| $M_i$ | 4 | 2 | 4 | 1 | 5 | 3 | 4 |
| $E_i$ | 35 | 30 | 40 | 10 | 60 | 45 | 50 |

1. **All pairs:** $n_0=21$.
2. **Ties in $M$:** pairs 1–3, 1–7, 3–7 (all $M=4$) → neither concordant nor discordant.
3. **Discordant:** 1–6 ($M$: 4>3, $E$: 35<45) and 3–6 ($M$: 4>3, $E$: 40<45) → $n_-=2$.
4. **Concordant:** all the rest, $n_+=21-3-2=16$.
5. **Tie corrections:** one tie group of size 3 in $M$ → $n_M=\tfrac12\cdot3\cdot2=3$. No ties in $E$ → $n_E=0$.
6. $\tau=\dfrac{16-2}{\sqrt{21-3}\sqrt{21-0}}=\dfrac{14}{19.44}=0.720$.
7. **Test:** $z=2.195$ (tie-adjusted), $p=0.028$ → **reject** $H_0$.

```r
cor(dat$Members, dat$Expense, method = "kendall")       # 0.7201
cor.test(dat$Members, dat$Expense, method = "kendall")  # z = 2.1954, p = 0.02814
# count pairs by hand
s <- outer(dat$Members, dat$Members, "-") * outer(dat$Expense, dat$Expense, "-")
c(concordant = sum(s[upper.tri(s)] > 0), discordant = sum(s[upper.tri(s)] < 0))   # 16, 2
```

Related: [[Spearman correlation]] · [[Pearson correlation]] · [[Contingency tables]]
