---
tags: [ma012, lecture/L05, type/test]
aliases: [Fisher z, Fisher transformation, arctanh transform]
lecture: L05
---

# Fisher z-transformation (Fisher 1915/1921)

**Hypotheses:** $H_0:\rho_{XY}=\rho_0$ for a given $\rho_0\in(-1,1)$; $H_1:\rho_{XY}\ne\rho_0$.
**Assumption:** a bivariate normal sample with $n\ge4$.

$$z=\operatorname{arctanh}r=\frac12\ln\frac{1+r}{1-r}\;\overset{as.}{\sim}\;N\!\left(\frac12\ln\frac{1+\rho_0}{1-\rho_0},\;\frac1{n-3}\right)$$

**Decision:** reject $H_0$ if
$$\sqrt{n-3}\left\lvert z-\tfrac12\ln\tfrac{1+\rho_0}{1-\rho_0}\right\rvert\ge u_{1-\alpha/2}.$$

**Why it is useful:**
- $z$ is approximately normal with a **stable variance** $1/(n-3)$, even when $r$ is close to ±1, where $r$ itself is very skewed.
- So it gives:
  - tests of $\rho=\rho_0\ne0$;
  - **confidence intervals** for $\rho$:
$$\tanh\!\left(z\pm\frac{u_{1-\alpha/2}}{\sqrt{n-3}}\right)$$

**Example** ($r=0.89$, $n=7$, $\rho_0=0$):
- $\sqrt4\cdot\operatorname{arctanh}(0.8896)=2.84>1.96$ → reject.
- CI: $\tanh(1.421\pm0.980)=(0.414,\,0.984)$ — exactly the CI reported by `cor.test`.

```r
z <- atanh(r); n <- 7
tanh(z + c(-1, 1) * qnorm(0.975) / sqrt(n - 3))
2 * pnorm(-abs(sqrt(n - 3) * (z - atanh(rho0))))     # p-value for H0: rho = rho0
```

**Spearman version:** $U^{Sp}=\sqrt{\frac{n-3}{1.06}}\operatorname{arctanh}(r^{Sp})\approx N(0,1)$ → [[Spearman correlation]].

Related: [[Pearson correlation]]
