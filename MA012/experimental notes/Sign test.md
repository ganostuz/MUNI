---
tags: [ma012, lecture/L03, type/test, exam/2]
aliases: [Znaménkový test, Sign-test]
lecture: L03
exam: 2
---

# Sign test

## Assumptions and hypotheses
- **Data:** a random sample from a **continuous** distribution with median $\tilde x$, i.e. $P(X<\tilde x)=P(X>\tilde x)=\tfrac12$.
- **Hypotheses:** $H_0:\tilde x=x_0$ vs $H_1:\tilde x\ne x_0$ (or one-sided).
- No symmetry is needed, which is its advantage over the [[Wilcoxon signed-rank test]].

## Algorithm
1. Compute the differences $Y_i=X_i-x_0$. **Remove zeros** and reduce $n$ accordingly.
2. Count the positive ones: $S^+=\#\{Y_i>0\}$.
3. Under $H_0$, every difference is positive with probability ½, so $S^+\sim\mathrm{Bin}(n,\tfrac12)$, with $ES^+=\frac n2$ and $\mathrm{Var}S^+=\frac n4$ → [[Binomial distribution]].

## Decision
**Exact (small $n$):**
- Reject if $S^+\le k_\alpha$ or $S^+\ge n-k_\alpha$.
- $k_\alpha$ is the largest $k$ with $P(S^+\le k)=\frac1{2^n}\sum_{i=0}^{k}\binom ni\le\frac\alpha2$.

**Asymptotic (large $n$, fine for $n\ge20$):**
$$U=\frac{S^+-n/2}{\sqrt{n/4}}=\frac{2S^+-n}{\sqrt n}\overset{as.}{\sim}N(0,1);\qquad\text{reject if }|U|\ge u_{1-\alpha/2}.$$

## Example: "guess one minute", $H_0:\tilde x=60$
| $X_i$ | 53 | 48 | 45 | 55 | 63 | 51 | 66 | 56 | 50 | 58 |
|---|---|---|---|---|---|---|---|---|---|---|
| $X_i-60$ | −7 | −12 | −15 | −5 | **+3** | −9 | **+6** | −4 | −10 | −2 |

1. $n=10$ and $S^+=2$.
2. **Exact:** $P(S^+\le1)=0.0107\le0.025$ but $P(S^+\le2)=0.0547>0.025$, so $k_{0.05}=1$. Reject only if $S^+\le1$ or $\ge9$. → **Not rejected.**
3. **Asymptotic:** $U=(4-10)/\sqrt{10}=-1.90$, $|U|<1.96$ → not rejected.
4. **Exact p-value:** $2P(S^+\le2)=0.109$.

```r
X <- c(53, 48, 45, 55, 63, 51, 66, 56, 50, 58)
S_plus <- sum(X > 60); n <- sum(X != 60)
binom.test(S_plus, n, p = 0.5)              # p-value = 0.1094, CI for P(X > 60)
qbinom(c(0.025, 0.975), size = 10, prob = 0.5)
```

## Notes
- Use it for **skewed** distributions, where the t-test gives biased p-values.
- It has **low power** (it uses only the signs), so it needs large samples. In the example, Wilcoxon rejects but the sign test doesn't.
- **Paired version:** apply it to the differences $W_i-V_i$ → [[Paired vs two-sample design]].

Related: [[Wilcoxon signed-rank test]] · [[Order statistics and ranks]] · [[Central limit theorem]]
