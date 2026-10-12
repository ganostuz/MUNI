## Parametric vs non parametric methods
### Prametric
- have strong assumption of probability distribution
- Based on the observed data (𝑋1, … , 𝑋𝑛)′, estimations of the parameters are calculated and tests for these parameters are performed.
### Non parametric
- no parametric assumptions about probability distributions
- Division:
	- Rank-based tests (pořadové testy): nonparametric tests based on ranks of random variables in random sample.
	-  Permutation tests (permutační testy): nonparametric tests where the probability distribution of a test statistic is obtained by calculation all possible values under rearrangement of the sample.

## Ordered sample
- we take random sample and order it into **non decreasing** sequence.
- it is a vector
- notation is $X_{(i)}$ **i-th order statistics**
- density of order statistics: $f_{X_{(r)}}(x) = \frac{n!}{(r-1)!\,(n-r)!} \, f_X(x) \, \bigl[F_X(x)\bigr]^{r-1} \bigl[1 - F_X(x)\bigr]^{n-r}$
- $X_{(1)}$  is sample minimum
- $X_{(n)}$  is sample maximum
- **tied observations** are observations with same value
- If a random sample follows continuous distribution, then there are no ties $P(X_j=X_i)=0$ for any i and j
- Ordered statistics are neither mutally independent nor identically distributed. Meaning every ordered statistic, has different distribtuion than original data, and different distribution than other ordered statistics.
- **TODO**: l
	- look into why ordered statistics have all different distribution and look at picture
## Ranks (rank statistics)
- **Rank** is defined to be *order* in Ordered sample. Basically position of random variable in ordered sample.
- $R_i=1+\#(j:X_j<X_i)$  and $X_i$
- Example: we have $X_2$ that was evaluated as 2 and we have *3* other random variables with values lower than $X_2$ than rank of $X_2$ is *4*.
- sum or ranks $n(n+1)/2$
- if there are ties present we need adjustment:
	- **average ranking**:  $R_i=1+\#(j:X_j<X_i) + 1/2\#(j!=i:X_J=X_i)$ 
	- standard competition ranking: $R_i = R_j = 1 + \#(j: X_j<x_i)$
	- modified competition ranking: $R_i=\#(j:X_j<X_i)$
	- dense ranking
	- random ranking
-  in practice only average ranking because others break things theory is using(fixed sum) 
## Sign test
- test statistic has **binomial probability** distribution
	- 1 or 0, multiples times, independent
	- E$(S^+) = n/2$
	- $Var(S^+) = n/4$
	- Recall **Moivre-Laplace theorem**
- recipe:
	- 1. calculate $Y_i= X_i-x_0$ where $x_0$ is tested value
	- 2. compute $S^+$, aka number of times where $Y_i$ is positive
- there is difference in computation with small n and large n, with large we use moivre-laplace theorem
- in practice if we have case when $X_i = x_0$ than we first delete all such observations and contine computation,  we call that sample *reduced sample*
- low power
## Wilcoxon signed-rank test
- we are assuming random sample from a probability distribution $F_X$ (some distribution) symmetric about some point (median if continous)
- basically test whether my guess is said point of symmetry
- recipe:
	- 1. Calculate the differences $Y_i = X_i - x_0$.
	- 2. Sort them in nondecreasing order according to their absolute value:
   $$|Y|_{(1)} \le |Y|_{(2)} \le \dots \le |Y|_{(n)}.$$
	- 3. Denote by $R_i$ the rank of $|Y_i|$ in this nondecreasing sequence.
	- 4. Calculate the signed-rank sum $T = \sum_{i=1}^{n} R_i \operatorname{sgn}(Y_i)$, the rank sums $T^+ = \sum_{Y_i > 0} R_i$ and $T^- = \sum_{Y_i < 0} R_i$, and the asymptotic statistic
   $$U = \frac{T^+ - \mathrm{E}(T^+)}{\sqrt{\operatorname{Var}(T^+)}}.$$
- has its own probability distribution
- one sample t-test is analogy
- for ties use average ranking
- TO ASK: what about discrete assymetric data? aka n=2k+1
## Mann-Whitney test/Wilcoxon rank-sum test
- test if distributions are identical
	- $h_0$: identical distributions
	- $h_1$: different distributions
- recipe:
	- 1. Join both samples:
   $$(Z_1, \dots, Z_{m+n}) = (X_1, \dots, X_m, Y_1, \dots, Y_n).$$
	- 2. Sort the combined sample in nondecreasing order:
   $$Z_{(1)} \le Z_{(2)} \le \dots \le Z_{(m+n)}.$$
	- 3. Denote by $(R_1, \dots, R_m)$ the ranks of $(X_1, \dots, X_m)$ and by $(S_1, \dots, S_n)$ the ranks of $(Y_1, \dots, Y_n)$ in the combined ordered sample.
	- 4. Calculate the rank sums for each sample, $T_1 = \sum_{i=1}^{m} R_i$ and $T_2 = \sum_{j=1}^{n} S_j$.
	- 5. Calculate the Mann–Whitney $U$ statistics:
   $$U_1 = T_1 - \frac{m(m+1)}{2}, \qquad U_2 = T_2 - \frac{n(n+1)}{2}.$$

The test statistic is $U_{\mathrm{MW}} = \min\{U_1, U_2\}$, and the asymptotic test statistic is
$$U = \frac{U_{\mathrm{MW}} - \mathrm{E}(U_{\mathrm{MW}})}{\sqrt{\operatorname{Var}(U_{\mathrm{MW}})}}.$$
- If $𝑈_{MW }≤ 𝑤_𝛼(𝑚, 𝑛)$,
- where $𝑤_𝛼(𝑚, 𝑛)$ is critical value of the Mann-Whitney test, $𝐻_0$ is rejected at
- the significance level 𝛼.
- Under $𝐻_0, 𝑈 ∼ N(0, 1)$.
- If $|𝑈| ≥ 𝑢_1−𝛼/2, 𝐻_0$ is rejected at the asymptotic significance level 𝛼.
- in case of ties we use average ranks
- $E(U)=\frac{1}{2}mn$
- $Var(U)=\frac{1}{12}mn(m+n+1)$
## Kruskal-Wallis test/Kruskall-Wallis rank sum test
- non paramaetric analogy to 1 way anova
- we need one variable at least ordinal and one categorical grouping var
- The observations in the group of 𝑖,th level,$(𝑌_{𝑖1}, … ,𝑌_{𝑖 𝑛_𝑖} )$, form a random sample of size $𝑛_𝑖$ from a probability distribution with $CDF$ $𝐹_𝑖$
- We still need homogenity of variance
- $H_0:$ implies, that the mean ranks of all groups are the same.
- $H_1$: at least one sample stochastically dominates an another sample.
- 1. Join all observations: $(Y_{11}, \dots, Y_{an_a})$.
- 2. Sort the combined sample in nondecreasing order:
   $$Y_{(1)} \le Y_{(2)} \le \dots \le Y_{(n)}, \qquad n = \sum_{i=1}^{a} n_i.$$
- 3. Denote by $R_{ij}$ the (mid)rank of $Y_{ij}$ in the combined sample.
- 4. Calculate the sums of ranks in each group:
   $$T_i = R_{i\cdot} = \sum_{j=1}^{n_i} R_{ij}, \qquad i = 1, \dots, a.$$

|   $A$    |        observations         |            ranks            |   size   | sum of ranks |
| :------: | :-------------------------: | :-------------------------: | :------: | :----------: |
|   $1$    | $(Y_{11}, \dots, Y_{1n_1})$ | $(R_{11}, \dots, R_{1n_1})$ |  $n_1$   |    $T_1$     |
| $\vdots$ |          $\vdots$           |          $\vdots$           | $\vdots$ |   $\vdots$   |
|   $i$    | $(Y_{i1}, \dots, Y_{in_i})$ | $(R_{i1}, \dots, R_{in_i})$ |  $n_i$   |    $T_i$     |
| $\vdots$ |          $\vdots$           |          $\vdots$           | $\vdots$ |   $\vdots$   |
|   $a$    | $(Y_{a1}, \dots, Y_{an_a})$ | $(R_{a1}, \dots, R_{an_a})$ |  $n_a$   |    $T_a$     |
|  total   |                             |                             |   $n$    |  $n(n+1)/2$  |
## Permutation tests
- TODO
