---
tags: [statistics, hypothesis-testing, cheatsheet]
---

# Statistical Tests – Cheatsheet

> [!important] General prerequisite
> All tests assume **independent observations**.
> Reject $H_0$ if p-value $< \alpha$ (usually $\alpha = 0.05$).

---

## 1. One-sample location (median / mean vs. value $m_0$)

### Sign test
> [!warning] Requirements
> - Continuous distribution (ties with $m_0$ are discarded)
> - No symmetry or normality needed

$H_0: \tilde{m} = m_0 \quad H_1: \tilde{m} \neq m_0$

$S = \#\{X_i > m_0\}$, under $H_0$: $S \sim \text{Bin}(n', \tfrac12)$, where $n' = n - \text{ties}$

Weakest assumptions, lowest power.

### Wilcoxon signed-rank test
> [!warning] Requirements
> - Continuous distribution, **symmetric** around its median

$H_0: \tilde{m} = m_0 \quad H_1: \tilde{m} \neq m_0$

$W^+ = \sum_{X_i > m_0} R_i$, where $R_i$ is the rank of $|X_i - m_0|$

Under $H_0$: $E W^+ = \frac{n(n+1)}{4}, \quad \operatorname{Var} W^+ = \frac{n(n+1)(2n+1)}{24}$

Paired data → apply to differences $D_i = X_i - Y_i$ with $m_0 = 0$.

### One-sample t-test
> [!warning] Requirements
> - Normal distribution (for large $n$ approximately valid by CLT)

$H_0: \mu = \mu_0$ — tests the **mean** (under normality mean = median)

$$T = \frac{\bar X - \mu_0}{S/\sqrt{n}} \sim t_{n-1}$$

Paired t-test = this test applied to differences $D_i$.

---

## 2. Normality of data

> [!note]
> $H_0$: data come from a normal distribution.
> Small $n$ → rarely rejects; huge $n$ → rejects even trivial deviations.

### Shapiro–Wilk test
> [!warning] Requirements
> - $3 \le n \le 5000$

$$W = \frac{\left(\sum a_i X_{(i)}\right)^2}{\sum (X_i - \bar X)^2}, \quad W \in (0, 1]$$

Small $W$ → reject normality. Generally the most powerful normality test (default choice).

### Lilliefors test
> [!warning] Requirements
> - $n \ge 100$

Kolmogorov–Smirnov test with $\mu, \sigma$ estimated from data:

$$D = \sup_x \left| F_n(x) - \Phi\left(\frac{x - \bar X}{S}\right) \right|$$

Uses its own critical values (classical KS table would be too conservative).

---

## 3. Homogeneity of variance ($k$ groups)

> [!note]
> $H_0: \sigma_1^2 = \sigma_2^2 = \dots = \sigma_k^2 \quad H_1:$ at least one variance differs

### Bartlett test
> [!warning] Requirements
> - Precise normality

Statistic $\approx \chi^2_{k-1}$.
Very sensitive to non-normality — rejection may only signal non-normal data. Use when normality is confirmed.

### Levene test
> [!warning] Requirements
> - $n \ge 3$

One-way ANOVA on $Z_{ij} = |X_{ij} - \bar X_i|$, so $F \sim F_{k-1,\,N-k}$.
Robust to non-normality. Brown–Forsythe variant uses median instead of $\bar X_i$ (even more robust).

---

## 4. Two samples

### Two-sample t-test
> [!warning] Requirements
> - Homogeneity of variance
> - Normality of data in both groups

$H_0: \mu_1 = \mu_2 \quad H_1: \mu_1 \neq \mu_2$

$$T = \frac{\bar X - \bar Y}{S_p\sqrt{\frac{1}{n_1} + \frac{1}{n_2}}} \sim t_{n_1+n_2-2}, \qquad S_p^2 = \frac{(n_1-1)S_1^2 + (n_2-1)S_2^2}{n_1+n_2-2}$$

Unequal variances → **Welch t-test**. Paired samples → paired t-test.

### Two-sample ANOVA
> [!warning] Requirements
> - Homogeneous variance
> - Normal data

$H_0: \mu_1 = \mu_2$. With $k = 2$ identical to pooled t-test: $F = T^2$, same p-value.
Not to be confused with *two-way* ANOVA (two factors + interaction).

### Mann–Whitney test / Wilcoxon rank-sum test
> [!warning] Requirements
> - Two independent samples, at least ordinal data
> - No normality needed

$H_0$: identical distributions $\quad H_1$: distributions differ (typically $P(X > Y) \neq \tfrac12$)

$U = R_1 - \frac{n_1(n_1+1)}{2}$, where $R_1$ is the rank sum of sample 1 in the pooled ranking

Under $H_0$: $E U = \frac{n_1 n_2}{2}, \quad \operatorname{Var} U = \frac{n_1 n_2 (n_1+n_2+1)}{12}$

Nonparametric alternative to two-sample t-test. Same shape of distributions → tests equality of medians.

---

## 5. $k$ samples ($k \ge 2$)

### One-way ANOVA
> [!warning] Requirements
> - Homogeneous variance
> - Normal data in each group

$H_0: \mu_1 = \dots = \mu_k \quad H_1:$ at least one mean differs

$$F = \frac{SS_B/(k-1)}{SS_W/(N-k)} \sim F_{k-1,\,N-k}$$

Rejection doesn't say *which* groups differ → post-hoc tests (Tukey HSD, Bonferroni).

### Kruskal–Wallis test
> [!warning] Requirements
> - Independent samples, at least ordinal data
> - No normality needed

$H_0$: mean ranks of all groups are the same (all groups come from the same distribution)
$H_1$: at least one sample stochastically dominates another sample

$$H = \frac{12}{N(N+1)} \sum_{i=1}^k \frac{R_i^2}{n_i} - 3(N+1) \approx \chi^2_{k-1}$$

Nonparametric alternative to one-way ANOVA. Post-hoc: Dunn's test.