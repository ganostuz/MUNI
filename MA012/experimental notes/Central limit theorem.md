---
tags: [ma012, type/concept]
aliases: [CLT, Asymptotic normality]
lecture: [L02, L03, L04]
---

# Central limit theorem (CLT)

> [!info] Statement (i.i.d. version)
> If $X_1,\dots,X_n$ are i.i.d. with mean $\mu$ and finite variance $\sigma^2$, then
> $$\frac{\bar X-\mu}{\sigma}\sqrt n\;\overset{as.}{\sim}\;N(0,1)\qquad(n\to\infty).$$
> **de Moivre–Laplace** is the special case for binomial counts → [[Binomial distribution]].

## Why it matters here
| Where | What becomes approximately normal | Rule of thumb |
|---|---|---|
| [[One-way ANOVA]] robustness | the group means $\bar Y_{i\cdot}$ | $n_i\ge15$ tolerates mild skewness |
| [[Sign test]] | $U=(2S^+-n)/\sqrt n$ | $n\ge20$ |
| [[Wilcoxon signed-rank test]] | $(T^+-ET^+)/\sqrt{\mathrm{Var}T^+}$ | $n\ge30$ |
| [[Mann-Whitney-Wilcoxon test]] | $(U-EU)/\sqrt{\mathrm{Var}U}$ | $m,n\ge20$ |
| [[Pearson chi-squared GOF test]] | each $(N_j-n_j)/\sqrt{n_j}$ | expected $n_j\ge5$ |

Wherever a test says "asymptotic", it relies on this kind of approximation, and it is only trustworthy for large enough samples. For small samples use **exact** distributions (tables, `binom.test`, `dsignrank`, `dwilcox`) or [[Permutation tests]].
