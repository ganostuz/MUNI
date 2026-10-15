---
tags: [ma012, lecture/L01, type/concept, exam/13]
aliases: [Statistical test, Null hypothesis, Alternative hypothesis, Critical region, Testing procedure]
lecture: L01
exam: 13
---

# Hypothesis testing

## The setup
- **History:** significance testing began with Pearson, Gosset and Fisher (around 1900); the modern framework is Neyman & Pearson (1933).
- **Hypothesis** = a pair of statements:
  - $H_0$ (**null**) is the statement being tested, often "no effect / no difference";
  - $H_1$ (**alternative**) is what we test against it, i.e. "an effect".
- **Test** = a decision based on a random sample. Either:
  - **reject** $H_0$ in favour of $H_1$, or
  - **do not reject** (fail to reject) $H_0$. This is *not* "accept $H_0$", only "not enough evidence".
- **Test statistic** $T$: a function of the sample whose distribution **under $H_0$** is known. The observed value is $T^*$.

> [!important] Testing procedure (p-value form)
> 1. State $H_0$ and $H_1$ from the research question.
> 2. Check the assumptions; choose a test statistic $T$ with known distribution under $H_0$.
> 3. Choose the [[Type I and II errors and power|significance level]] $\alpha$ (usually 0.05) **before** looking at the data.
> 4. Compute the observed value $T^*$.
> 5. Compute the [[p-value]].
> 6. If $p<\alpha$ (the slides also accept $p\le\alpha$), reject $H_0$ in favour of $H_1$.

## Three equivalent decision rules
| Rule | Reject $H_0$ when |
|---|---|
| [[p-value]] | $p\le\alpha$ |
| **critical region** $W$ | $T^*\in W$ |
| [[Confidence interval]] | the $H_0$ value $\theta_0\notin CI_{1-\alpha}$ |

**Critical region** = the values of $T$ that lead to rejection, chosen so that $P(T\in W\mid H_0)=\alpha$. It is bounded by quantiles of the null distribution:

| $H_1$ | $W$ |
|---|---|
| two-sided ($\ne$) | $(-\infty,q_{\alpha/2})\cup(q_{1-\alpha/2},\infty)$ |
| left-sided ($<$) | $(-\infty,q_{\alpha})$ |
| right-sided ($>$) | $(q_{1-\alpha},\infty)$ |

## One-sided vs two-sided (L01 example: diets A vs C)
- Welch test: $t=-2.846$, $df=48.86$.
  - $H_1:\mu_A\ne\mu_C$: $p=0.00645$.
  - $H_1:\mu_A<\mu_C$: $p=0.0032$ (half, since $t$ lies in the direction of $H_1$).
  - $H_1:\mu_A>\mu_C$: $p=0.9968$ — wrong direction; an unrealistic hypothesis here.
- Choose the direction **from the research question before seeing the data**.
- Statistics measuring a "discrepancy" ($\chi^2$, $F$, Kruskal-Wallis, K-S) are **right-tailed** by construction.

## Questions the slides ask you to answer
- *Is the omnibus F-test one- or two-sided?* One-sided (right tail), even though $H_1$ ("some means differ") has no direction.
- *Read the p-value and decide:* $p=0.00645<0.05$ → reject $H_0$: diets A and C have different mean weight loss.

> [!check] Exam checklist
> - [ ] Define $H_0$, $H_1$, test statistic, critical region, significance level, p-value.
> - [ ] Make the same decision three ways: p-value, critical region, CI.
> - [ ] Explain why "not rejected" ≠ "$H_0$ is true".

Related: [[Type I and II errors and power]] · [[Multiple testing problem]] · [[00 Choosing a test]] · [[t-tests]]
