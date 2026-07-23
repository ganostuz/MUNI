# Properties and definition of variance
## Definition
The second central [[moment]] is called the **variance** and is denoted as:
$$DX = E(X - EX)^2$$
The number $\sigma_X = \sqrt{DX}$ is called the **standard deviation** of the random variable $X$ (Standard Deviation).
## Properties
Let $X, X_1, X_2$ be random variables defined on $(\Omega, \mathcal{A}, P)$ with finite second moments, and let $a, a_1, a_2 \in \mathbb{R}$. Then:  
- $DX \ge 0$
- $DX = EX^2 - (EX)^2$
- $\text{If } P(X = a) = 1 \implies DX = 0$
- $D(a_1 + a_2X) = a_2^2 DX$ 
- $\text{Let } X_1, X_2 \text{ be independent random variables} \implies D(X_1 + X_2) = DX_1 + DX_2$

## Calculation
Oftentimes it is computes as $DX = EX^2 - (EX)^2$ not by standard definition, as it is easier.

## Distributions
For $DX$ of famous distributions refer to this [[Statistical distributions|table]].

---
## Requirements
[[Expected value]]