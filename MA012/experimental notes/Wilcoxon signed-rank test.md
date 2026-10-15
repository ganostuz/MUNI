---
tags: [ma012, lecture/L03, type/test, exam/2]
aliases: [Signed-rank test, One-sample Wilcoxon test, Jednovýběrový Wilcoxonův test]
lecture: L03
exam: 2
---

# Wilcoxon signed-rank test

**History:** Wilcoxon (1945), popularised by Siegel.
**Use:** a test of the **location** of one sample, or of paired samples (on the differences), for at least ordinal data.

## Assumptions and hypotheses
- **Data:** a random sample from a distribution **symmetric** around $\mu$.
- **Hypotheses:** $H_0$: symmetric around $\mu=x_0$; $H_1$: symmetric around some $\mu\ne x_0$.
- With a continuous distribution, $\mu$ is the median, so $H_0:\tilde x=x_0$ (and also the mean, if it exists).

> [!warning] Asymmetric data
> If the distribution is asymmetric, $H_0$ can be rejected even when $\tilde x=x_0$. Use the [[Sign test]] instead.

## Algorithm
1. Compute the differences $Y_i=X_i-x_0$.
2. Rank their **absolute values** $\lvert Y_i\rvert$, giving ranks $R_i$ (average ranks for ties).
3. Sum the ranks by sign:
$$T^+=\sum_{Y_i>0}R_i,\qquad T^-=\sum_{Y_i<0}R_i,\qquad T=\sum R_i\,\mathrm{sgn}(Y_i)=T^+-T^-$$

## Distribution under $H_0$
- $T^++T^-=\frac{n(n+1)}2$ and $T^+=\frac T2+\frac{n(n+1)}4$.
- $E T^+=\frac{n(n+1)}4$ and $\mathrm{Var}\,T^+=\frac{n(n+1)(2n+1)}{24}$.
- $ET=0$ and $\mathrm{Var}\,T=\frac{n(n+1)(2n+1)}6$.
- $T^+$ has its own discrete distribution: `dsignrank(x, n)`.

## Decision
- **Exact:** reject if $\min\{T^+,T^-\}\le w_\alpha(n)$ (table critical value).
- **Asymptotic** (good for $n\ge30$):
$$U=\frac{T^+-ET^+}{\sqrt{\mathrm{Var}\,T^+}}\overset{as.}{\sim}N(0,1);\qquad\text{reject if }|U|\ge u_{1-\alpha/2}.$$

## Example: "guess one minute", $H_0:\tilde x=60$
| $X_i$ | 53 | 48 | 45 | 55 | 63 | 51 | 66 | 56 | 50 | 58 |
|---|---|---|---|---|---|---|---|---|---|---|
| $Y_i$ | −7 | −12 | −15 | −5 | +3 | −9 | +6 | −4 | −10 | −2 |
| $R_i$ | 6 | 9 | 10 | 4 | **2** | 7 | **5** | 3 | 8 | 1 |

1. $T^+=2+5=7$, $T^-=48$ (check: $7+48=55=\frac{10\cdot11}2$ ✔), $T=-41$.
2. $ET^+=27.5$ and $\mathrm{Var}\,T^+=96.25$ (sd 9.81).
3. $U=(7-27.5)/9.81=-2.09$, and $|U|>1.96$ → **reject $H_0$**.
4. **Exact:** $w_{0.05}(10)=8$, since $P(T^+\le8)=0.0244\le0.025<P(T^+\le9)=0.0322$. Then $\min(7,48)=7\le8$ → reject. Exact $p=2P(T^+\le7)=0.037$.

```r
wilcox.test(X, mu = 60)                     # V = 7 (= T+), p-value = 0.03711 (exact)
wilcox.test(after, before, paired = TRUE)   # paired version
dsignrank(0:55, n = 10); psignrank(7, 10); qsignrank(0.025, 10)
```

## Notes
- **Zeros:**
  - reduced-sample procedure: remove them;
  - Pratt (1959): rank them, but use $\mathrm{sgn}(0)=0$ in $T$.
- **Ties:** use average ranks, which changes the null distribution. R then switches to the normal approximation with an adjusted variance.
- **Power:** greater than the sign test, because it uses the size of the differences (through their ranks), not just their signs.
- **Parametric twin:** the one-sample t-test → [[t-tests]].

Related: [[Sign test]] · [[Order statistics and ranks]] · [[Paired vs two-sample design]] · [[Mann-Whitney-Wilcoxon test]]
