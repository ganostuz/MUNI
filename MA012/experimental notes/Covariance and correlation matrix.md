---
tags: [ma012, lecture/L05, type/concept, exam/5]
aliases: [Variance-covariance matrix, Correlation matrix, Correlogram, Sample covariance matrix, Kovarianční matice]
lecture: L05
exam: 5
---

# Variance-covariance matrix, correlation matrix and correlogram

## Data layout
$n$ observations of $m$ variables form the data matrix
$$\mathbf Z=(Z_{ij})_{n\times m}$$
- **rows** = individual $m$-dimensional observations;
- **columns** = random samples of the variables $Z_1..Z_m$.

## Definitions
> [!info] Definitions
> - **Sample variance-covariance matrix:** $\mathbf S=\{S_{Z_iZ_j}\}_{i,j=1}^m$
> - **Sample correlation matrix:** $\mathbf R=\{r_{Z_iZ_j}\}_{i,j=1}^m$

| | $\mathbf S$ | $\mathbf R$ |
|---|---|---|
| size | $m\times m$, symmetric | $m\times m$, symmetric |
| diagonal | sample **variances** $S^2_{Z_j}$ | **ones** |
| off-diagonal | covariances (unit-dependent) | correlations in $[-1,1]$ (unitless) |
| definiteness | positive semidefinite (positive definite when no variable is a linear combination of others and $n>m$) | same |

**Relation:** $\mathbf R=\mathbf D^{-1/2}\mathbf S\,\mathbf D^{-1/2}$, where $\mathbf D=\mathrm{diag}(\mathbf S)$.

**How to read them:**
- $\mathbf S$ shows spread and joint variation in the original units.
- $\mathbf R$ shows the strength and direction of the linear relations, comparable across variables.
- The eigenvectors and eigenvalues of $\mathbf S$ (or $\mathbf R$) define the principal components → [[Principal component analysis]].

## Example 1 (households)
```r
cov(dat)
#          Members   Income  Expense
# Members    1.905   50.238   19.643
# Income    50.238 1561.905  623.810
# Expense   19.643  623.810  255.952
sqrt(diag(cov(dat)))      # 1.38, 39.52, 16.00 (standard deviations)
round(cor(dat), 2)
#          Members Income Expense
# Members     1.00   0.92    0.89
# Income      0.92   1.00    0.99
# Expense     0.89   0.99    1.00
ggcorrplot::cor_pmat(dat) # p-values: M-I 0.003, M-E 0.007, I-E 0.000
```

**Check one entry:** $19.643/(1.380\cdot16.00)=0.89$ ✔.

## Correlogram
- A heat-map of $\mathbf R$: colour = sign and strength, often with the values printed.
- It can hide non-significant cells using the p-value matrix.
- Shows at a glance which variables cluster together.
- A **scatterplot matrix** (`GGally::ggpairs`) shows all pairwise plots, densities and correlations together.

```r
GGally::ggpairs(dat)
GGally::ggcorr(dat, label = TRUE)
ggcorrplot::ggcorrplot(cor(dat), lab = TRUE, p.mat = ggcorrplot::cor_pmat(dat))
cor(dat, method = "spearman")       # rank version
```

| Statistic | R |
|---|---|
| $\bar X$, $S_X^2$, $S_X$ | `mean`, `var`, `sd` |
| $S_{XY}$, $r_{XY}$, test | `cov(X, Y)`, `cor(X, Y)`, `cor.test(X, Y)` |
| $\mathbf S$, $\mathbf R$ | `cov(Z)`, `cor(Z)` |

Related: [[Pearson correlation]] · [[Partial correlation]] · [[Multiple correlation]]
