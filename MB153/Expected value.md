
Let $X, X_1, X_2$ be independent random variables defined on $(\Omega, \mathcal{A}, P)$ and let $a, a_1, a_2 \in \mathbb{R}$. Then this holds true:
  - $EX \text{ existuje} \iff E|X| \text{ existuje}$
  - $P(X = a) = 1 \implies EX = a$
  - $\text{If exist } EX_1, EX_2 \implies E(a_1X_1 + a_2X_2) = a_1EX_1 + a_2EX_2$
  - $\text{If exist } EX_1, EX_2 \text{ and holds } X_1 \le X_2 \implies EX_1 \le EX_2$
  - $\text{Let } |X_1| \le X_2 \text{ and } EX_2 \text{ exists} \implies EX_1 \text{ exists}$
  - $P(X \ge 0) = 1 \implies EX \ge 0$
## EX of a product of independent random variables
Let $X_1, \dots, X_n$ be independent random variables defined on $(\Omega, \mathcal{A}, P)$ and let the expected values $EX_1, \dots, EX_n$ exist. Then: $$E \left( \prod_{i=1}^n {X_i} \right) = \prod_{i=1}^n {EX_i}$$
## Calculation
In case of discrete random variable, where $p(x)$ is probability of x:
$$
EX=\sum_{x\in M}xp(x)
$$
In case of continuous random variable, where $f(x)$ is probability density of x:
$$
EX=\int_{-\infty}^{\infty}xf(x)dx
$$
## Distributions
For EX of famous distributions there are derived formulas, look into this [[Statistical distributions|table]].

