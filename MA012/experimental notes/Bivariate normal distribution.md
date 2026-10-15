---
tags: [ma012, lecture/L05, type/distribution]
aliases: [N2, Bivariate Gaussian, Multivariate normal]
lecture: L05
---

# Bivariate normal distribution $N_2(\boldsymbol\mu,\Sigma)$

$$f(y_1,y_2)=\frac1{2\pi\sqrt{\det\Sigma}}\exp\!\left(-\tfrac12(\mathbf y-\boldsymbol\mu)'\Sigma^{-1}(\mathbf y-\boldsymbol\mu)\right),\qquad \det\Sigma=(1-\rho^2)\sigma_1^2\sigma_2^2$$

| Parameter | Meaning |
|---|---|
| $\boldsymbol\mu=(\mu_1,\mu_2)'$ | vector of expected values |
| $\Sigma=\begin{pmatrix}\sigma_1^2&\rho\sigma_1\sigma_2\\\rho\sigma_1\sigma_2&\sigma_2^2\end{pmatrix}$ | variance-covariance matrix |
| $\rho$ | Pearson correlation of $Y_1,Y_2$ |

**Properties:**
- **Contours** of the density are concentric **ellipses** centred at $\boldsymbol\mu$.
- The **principal axes of the ellipses point along the eigenvectors of $\Sigma$**. This is the idea behind [[Principal component analysis]].
- Linear transformations, marginal distributions and conditional distributions are all normal again.
- For this distribution, $\rho=0$ ⇔ independence (not true in general → [[Correlation pitfalls]]).
- It is the **assumption** behind the t-test of [[Pearson correlation]] and the [[Fisher z-transformation]].

```r
MASS::mvrnorm(200, mu = c(1, 0), Sigma = matrix(c(1, 0.5, 0.5, 4), 2))
ggplot(dat, aes(x, y)) + geom_point() + stat_ellipse(level = 0.95, type = "norm")
```

Related: [[Normal distribution]] · [[Covariance and correlation matrix]]
