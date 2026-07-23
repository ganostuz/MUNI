
Statistical distributions are used to approximate probability of some event occurring if conditions, in which distribution is used, are met. 
-  $f(x)$ is if probability or [[probability density function]].
- $F(X\leq x)$ is [[cumulative probability function]], returns probability of values lower or equal to x
- $\mu$ or *EX* is is [[Expected value]] of distribution
- $\sigma^2$ or *DX* is [[Variance]] of distribution
---
# Table of distributions

Density / probability function and Cumulative probability function of famous statistical distributions.

| name                       | $f(x)$                                                               | $F(X\leq x)$            | $\mu$               | $\sigma^2$                  |
| -------------------------- | -------------------------------------------------------------------- | ----------------------- | ------------------- | --------------------------- |
| [[poisson]]                | $\frac{e^{-\lambda}\lambda^{x}}{x!}$                                 | $\sum_{x\leq X}{} f(x)$ | $\lambda$           | $\lambda$                   |
| [[bernouli]] / alternative | $\theta$                                                             | $\sum_{x\leq X}{} f(x)$ | $\theta$            | $\theta(1-\theta)$          |
| [[binomial]]               | $\binom{n}{x}\theta^x(1-\theta)^{n-x}$                               | $\sum_{x\leq X}{} f(x)$ | $n\theta$           | $n\theta(1-\theta)$         |
| [[geometric]]              | $(1-\theta)^x\theta$                                                 | $\sum_{x\leq X}{} f(x)$ | $\frac{1}{\theta}$  | $\frac{1-\theta}{\theta^2}$ |
| [[exponential]]            | $\lambda e^{-\lambda x}$                                             | $1-e^{-\lambda x}$      | $\frac{1}{\lambda}$ | $\frac{1}{\lambda^2}$       |
| [[normal]]                 | $\frac{1}{\sigma\sqrt{2\pi}}^{-\frac{1}{2}(\frac{x-\mu}{\sigma})^2}$ |                         | $\mu$               | $\sigma^2$                  |
| [[uniform]]                | $\frac{1}{b-a}$                                                      | $\frac{x-a}{b-a}$       | $\frac{a+b}{s}$     | $\frac{(b-a)^2}{12}$        |

---
TODO:
- student
- beta
- gamma

