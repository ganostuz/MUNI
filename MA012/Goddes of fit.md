---
title: "Statistics II · L4 · Goodness-of-fit tests (concepts + R)"
course: Statistics II (MUNI)
lecture: 4
tags: [statistics, goodness-of-fit, R, hypothesis-testing]
aliases: [GOF tests in R]
---

# Goodness-of-fit tests — understand, compute, use in R

> [!abstract] One idea behind the whole lecture
> Every goodness-of-fit (GOF) test follows the same three steps:
> 1. **Measure the discrepancy** between what the data show and what the model $F_0$ predicts.
> 2. **Know how big that discrepancy usually is** when the model is true (its null distribution).
> 3. If the observed discrepancy is **unusually large**, reject the model.
>
> The tests differ only in *how* they measure the discrepancy:
>
> | Test | Compares | Discrepancy |
> |---|---|---|
> | Pearson $\chi^2$ | **counts** in bins | standardized squared differences |
> | Kolmogorov–Smirnov | **CDFs** | largest vertical gap |
> | Cramér–von Mises / Anderson–Darling | **CDFs** | average squared gap (AD stresses the tails) |
> | Shapiro–Wilk | **ordered data** vs normal order statistics | straightness of the Q-Q plot |
> | D'Agostino / Anscombe–Glynn / Jarque–Bera | **shape** | skewness and/or kurtosis vs normal values |

---

## 1. The logic of GOF testing

### Hypotheses

- $H_0$: the data come from $F_0$ (or from the family, e.g. "some normal").
- $H_1$: they don't.

### Two situations — keep them apart

1. **Fully specified** $F_0$, e.g. $\mathrm{Bin}(5,0.5)$ or $\mathrm{Ex}(0.04)$. Nothing is estimated.
2. **Family with unknown parameters**, e.g. "exponential" or "normal". You estimate the parameters from the **same** data.

   This makes the fitted model look better than it really is. Every test must correct for this:
   - $\chi^2$ test: subtract degrees of freedom.
   - Normality: use Lilliefors instead of K-S.

### Interpreting the result

- A **small p-value** means the data are unlikely under the model → reject.
- A **large p-value** means there is *no evidence against* the model. It does **not** prove the model is correct.
- **Sample size matters:**
  - Small $n$: tests have low power and real deviations go undetected.
  - Large $n$: even tiny, practically irrelevant deviations become "significant".
  - So **always look at plots too** (histogram, ECDF, Q-Q plot).

---

## 2. Pearson's chi-squared test

### 2.1 Intuition

1. Sort the observations into $k$ categories (values or intervals).
2. If the model is right, category $j$ should contain about $n_j = n p_j$ observations, where $p_j = P(X\in A_j)$ under $H_0$.
3. Compare these **expected** counts $n_j$ with the **observed** counts $N_j$.

> [!warning] Notation in this course
> $N_j$ = **observed** (capital, random) · $n_j$ = **expected** (small, $= np_j$).

### 2.2 Why the statistic looks the way it does

- Each $N_j\sim\mathrm{Bin}(n,p_j)$, so it has mean $np_j$ and variance $np_j(1-p_j)\approx n_j$ when $p_j$ is small.
- Standardizing gives $U_j=(N_j-n_j)/\sqrt{n_j}\approx N(0,1)$. In R, these $U_j$ are the **Pearson residuals**.
- Summing their squares:

$$
K=\sum_{j=1}^k\frac{(N_j-n_j)^2}{n_j}=\sum_{j=1}^k\frac{N_j^2}{n_j}-n
$$

- A sum of $k$ squared standard normals would be $\chi^2(k)$.
- But the counts are **jointly multinomial**: they must sum to $n$. One constraint costs one degree of freedom.

> [!important] Result
> $K\overset{as.}{\sim}\chi^2(k-1-m)$, where $m$ = number of parameters estimated from the data.
>
> Reject $H_0$ if $K\ge\chi^2_{1-\alpha}(k-1-m)$. The test is **one-sided (right tail)**, because only large discrepancies speak against $H_0$.

**Intuition for "$-m$":**
- Estimating a parameter bends the model towards the data.
- This makes $K$ systematically smaller.
- Each estimated parameter "uses up" one degree of freedom.

| $H_0$ | $m$ |
|---|---|
| fully specified (e.g. $\mathrm{Bin}(5,0.5)$) | 0 |
| Poisson / exponential, rate estimated | 1 |
| normal, $\mu$ and $\sigma$ estimated | 2 |

### 2.3 When the χ² approximation is trustworthy

The approximation relies on the normal approximation of each count, so **expected** counts must not be too small:

- **(a) Classic rule:** all $n_j\ge5$.
- **(b) Yarnold's rule:**
  - Let $q$ = fraction of categories with $n_j<5$.
  - Require all $n_j\ge5q$, and no $n_j=0$.
- If neither rule holds, **merge neighbouring categories**.
  - For ordered categories, merge adjacent ones only.
  - Merging changes $k$, and therefore the df.
- **Yates's correction** is for very small tables: replace $(N_j-n_j)^2$ by $(\lvert N_j-n_j\rvert-\tfrac12)^2$.

### 2.4 Estimating the parameters

**Theory (the slides).** The *modified minimum $\chi^2$* method solves

$$\sum_j \frac{N_j}{p_j(\theta)}\frac{\partial p_j(\theta)}{\partial\theta_i}=0,\qquad i=1,\dots,m.$$

This is maximum likelihood for the **binned** data.

**Practice (what the slides actually do).** Use the ordinary MLE, e.g.:
- $\hat\lambda=1/\bar X$ for the exponential;
- $\hat\lambda=\bar X$ for the Poisson;
- $\hat\mu=\bar X$, $\hat\sigma$ for the normal.

Then use $df=k-1-m$.

### 2.5 Recipe

**Discrete distribution**
- Categories = the individual values.
- If the support is infinite (Poisson, geometric), make the **last category "≥ c"** so that $\sum p_j=1$.

**Continuous distribution**
- Categories = intervals $(t_{j-1},t_j]$, with $p_j=F_0(t_j)-F_0(t_{j-1})$.
- The first and last intervals must reach the ends of the support ($0$, $\pm\infty$).
- Number of intervals: $k\approx\sqrt n$ or $k\approx 1+\log_2 n$.

**Steps:**
1. Estimate the parameters if needed.
2. Compute $N_j$, $p_j$ and $n_j=np_j$.
3. Check rule (a) or (b); merge categories if needed.
4. Compute $K$, the df, and the p-value. Decide.

### 2.6 Example 1 in R — boys in 84 five-child families, $H_0:\mathrm{Bin}(5,0.5)$

```r
x <- 0:5
N <- c(3, 10, 22, 31, 14, 4)            # observed
n <- sum(N)                             # 84
p <- dbinom(x, size = 5, prob = 0.5)    # 1/32, 5/32, 10/32, ...
E <- n * p                              # expected: 2.625 13.125 26.25 26.25 13.125 2.625

# --- by hand ---
K  <- sum((N - E)^2 / E)                # 3.1238
df <- length(N) - 1 - 0                 # 5 (nothing estimated)
qchisq(0.95, df)                        # critical value 11.07
pchisq(K, df, lower.tail = FALSE)       # p = 0.681

# --- check the conditions ---
all(E >= 5)                             # FALSE (two cells have 2.625)
q <- mean(E < 5); all(E >= 5*q) && all(E > 0)   # TRUE: Yarnold is satisfied

# --- built-in ---
res <- chisq.test(N, p = p)             # warns "approximation may be incorrect" (checks rule a only)
res$expected; res$residuals             # residuals = U_j: which cells drive K
```

**Merging the sparse cells** ($\{0,1\}$ and $\{4,5\}$):

```r
N2 <- c(sum(N[1:2]), N[3], N[4], sum(N[5:6]))   # 13 22 31 18
p2 <- c(sum(p[1:2]), p[3], p[4], sum(p[5:6]))
chisq.test(N2, p = p2)                           # X-squared = 2.349, df = 3, p = 0.503
```

> [!success] Conclusion
> - $K=3.12$, $p=0.68$ (or $K=2.35$, $p=0.50$ after merging).
> - **Do not reject** $H_0$. The data are consistent with $P(\text{boy})=0.5$ and independent births.

### 2.7 Example 2 in R — waiting times of 70 clients, $H_0$: exponential

```r
breaks <- c(0, 3, 6, 9, 12, 15, 18, 21, Inf)    # last interval open -> probabilities sum to 1
N      <- c(14, 16, 10, 9, 8, 5, 3, 5)
n      <- sum(N)                                 # 70
mid    <- c(1.5, 4.5, 7.5, 10.5, 13.5, 16.5, 19.5, 22.5)

lambda <- 1 / (sum(N * mid) / n)                 # MLE 1/xbar = 1/8.914 = 0.1122
p      <- diff(pexp(breaks, rate = lambda))      # P(t_{j-1} < X <= t_j)
E      <- n * p                                  # 20.0 14.3 10.2 7.29 5.21 3.72 2.66 6.64

K  <- sum((N - E)^2 / E)                         # 4.79
m  <- 1                                          # lambda was estimated
df <- length(N) - 1 - m                          # 6
pchisq(K, df, lower.tail = FALSE)                # p = 0.569
qchisq(0.95, df)                                 # 12.59
```

> [!warning] R pitfall
> `chisq.test(N, p = p)` always uses $df=k-1$ (here 7). It does **not** know you estimated $\lambda$.
>
> With estimated parameters, take $K$ from `chisq.test(...)$statistic` (or compute it) and get the p-value yourself with `pchisq(K, k - 1 - m, lower.tail = FALSE)`.

> [!note] Why the slides first got $\sum n_j = 65.3$
> With the last interval $(21,24]$, the bins did not cover $(24,\infty)$, so $\sum p_j=0.932$. Always make the categories cover the whole support.

**Conclusion:** $K=4.8$, $df=6$, $p=0.57$. **The exponential model is not rejected.**

### 2.8 Templates for other common cases

```r
# Poisson with estimated lambda, categories 0,1,2,3,>=4
lam <- sum(x_vals * N) / sum(N)        # MLE = mean (x_vals = 0:4 here; for the open
                                       # top category, use the raw data mean if you have it)
p   <- c(dpois(0:3, lam), ppois(3, lam, lower.tail = FALSE))
# df = 5 - 1 - 1 = 3

# Normal with estimated mu, sigma, binned raw data x
br <- c(-Inf, quantile(x, seq(0.1, 0.9, 0.1)), Inf)   # or fixed cut points
N  <- table(cut(x, br))
p  <- diff(pnorm(br, mean(x), sd(x)))
# df = k - 1 - 2

# Small expected counts: Monte Carlo p-value instead of the chi^2 approximation
chisq.test(N, p = p, simulate.p.value = TRUE, B = 10000)   # only valid if nothing estimated
```

---

## 3. Empirical CDF and the Kolmogorov–Smirnov test

### 3.1 ECDF — the data's own CDF

$$\hat F(x)=\frac1n\sum_{i=1}^n\mathbb 1\{X_i\le x\}=\text{fraction of observations}\le x$$

**Shape:**
- a step function, jumping by $1/n$ at each observation (more if there are ties);
- right-continuous and non-decreasing, from 0 to 1.

**Why it is a good estimator:**
- $n\hat F(x)\sim\mathrm{Bin}(n,F(x))$, so $E\hat F(x)=F(x)$ and $\mathrm{Var}\,\hat F(x)=F(x)(1-F(x))/n\to0$.
- It converges to the true CDF $F$ (uniformly, by Glivenko–Cantelli).

```r
Fn <- ecdf(x)          # Fn is a function
Fn(20)                 # proportion of observations <= 20
plot(Fn); curve(pexp(x, rate = 0.04), add = TRUE, col = "red")
```

### 3.2 The K-S statistic: the biggest vertical gap

$$D=\sup_x\lvert\hat F(x)-F_0(x)\rvert$$

**Picture:** draw the ECDF staircase and the model CDF on the same plot. $D$ is the **largest vertical distance** between them.

**How to compute it:**
- The gap can only be largest right at a jump.
- Just before the $i$-th jump the ECDF equals $(i-1)/n$; just after, it equals $i/n$.
- So you only need to check those two values at each ordered observation $X_{(i)}$:

$$
D=\max_i\max\left\{\frac in-F_0(X_{(i)}),\;F_0(X_{(i)})-\frac{i-1}n\right\}
$$

```r
xs <- sort(x); n <- length(xs); i <- 1:n
F0 <- pexp(xs, rate = 0.04)
D  <- max(pmax(i/n - F0, F0 - (i - 1)/n))
```

### 3.3 Decision

- Under $H_0$, $\sqrt n\,D$ has (asymptotically) the **Kolmogorov distribution**. Thanks to the probability integral transform (§5), it is the same for every continuous $F_0$.
- Asymptotic p-value:

$$p\approx 2\sum_{k=1}^{\infty}(-1)^{k-1}e^{-2k^2(\sqrt n D)^2}$$

- Asymptotic critical values for $\sqrt n D$ (for $n\ge 30$), from $\sqrt{\tfrac12\ln(2/\alpha)}$:

| $\alpha$ | 0.10 | 0.05 | 0.01 |
|---|---|---|---|
| critical value for $\sqrt n D$ | 1.22 | **1.36** | 1.63 |

```r
t <- sqrt(n) * D
k <- 1:100
p_asym <- 2 * sum((-1)^(k - 1) * exp(-2 * k^2 * t^2))
crit   <- sqrt(0.5 * log(2 / 0.05)) / sqrt(n)    # critical value for D itself
```

> [!important] Assumptions of the one-sample K-S test
> - $F_0$ is **continuous**.
> - $F_0$ is **fully specified**. If you estimated parameters from the data, the p-value is wrong (too large).
> - **No ties.**

### 3.4 Example 3 — restart times of a network device (246 days), $H_0:\mathrm{Ex}(0.04)$

```r
ks.test(dat$time_restart, "pexp", rate = 0.04)
# D = 0.082674, p-value = 0.06927  + warning about ties
```

> [!check] Verify by hand
> - $\sqrt{246}\cdot0.0827=1.297$.
> - At 5%: $1.297<1.36$ → **not rejected**.
> - At 10%: $1.297>1.22$ → would be rejected. The result is borderline.
> - The series formula gives $p\approx2e^{-2\cdot1.297^2}\approx0.069$ ✔ (matches R).

**Reading the rest of the output:**
- **The ties warning:** many days had restart time exactly 0, which creates a jump of the ECDF at 0. The data are a mixture (a point mass at 0 plus a continuous part), so K-S is only approximate here.
- **Exponential Q-Q plot:** the top points lie **below** the identity line. The largest observed values are smaller than the model predicts, so the right tail is **lighter** than exponential.

```r
# Q-Q plot against Ex(0.04): theoretical quantiles on x, data on y
qqplot(qexp(ppoints(length(x)), rate = 0.04), sort(x),
       xlab = "Ex(0.04) quantiles", ylab = "empirical quantiles")
abline(0, 1, col = "red")
```

**Practice without the original data** — simulate a sample with zeros, then run the same code:

```r
set.seed(42)
x <- ifelse(runif(246) < 0.07, 0, rgamma(246, shape = 1.5, rate = 1.5/25))
ks.test(x, "pexp", rate = 0.04)
```

### 3.5 Two-sample K-S test

- **Question:** do two independent samples come from the same continuous distribution?
- **Statistic:** $D=\sup\lvert\hat F_X-\hat F_Y\rvert$, the largest gap between the two staircases.
- **Large-sample rule:** reject if

$$\sqrt{\frac{nm}{n+m}}\,D\ge\sqrt{\tfrac12\ln\tfrac2\alpha}$$

```r
ks.test(x, y)
plot(ecdf(x)); plot(ecdf(y), add = TRUE, col = "blue")
```

> [!tip] Interpreting a two-sample rejection
> A rejection says the distributions differ *somewhere*. That could be location, spread or shape. Look at the ECDF plot to see where.

---

## 4. Assessing normality

### 4.1 Why normality gets special treatment

- t-tests, ANOVA and regression assume normal errors.
- The parameters $\mu$ and $\sigma$ are almost never known, so they must be estimated.
- That rules out plain K-S and requires adapted tests.

### 4.2 Which tool to use

| Goal | Use |
|---|---|
| General-purpose normality test | **Shapiro–Wilk** (`shapiro.test`, $3\le n\le5000$) |
| Sensitivity to tails/outliers | **Anderson–Darling** (`nortest::ad.test`) |
| "K-S for normality" | **Lilliefors** (`nortest::lillie.test`) — never plain `ks.test` with $\bar x, s$ |
| Diagnose *why* it is not normal | skewness/kurtosis tests (`moments`), Q-Q plot |
| Visual first look | histogram + density, boxplot, Q-Q plot, Cullen–Frey plot |

### 4.3 Skewness and kurtosis — shape numbers

$$
b_1=\frac{\frac1n\sum(X_i-\bar X)^3}{\left[\frac1n\sum(X_i-\bar X)^2\right]^{3/2}},\qquad
b_2=\frac{\frac1n\sum(X_i-\bar X)^4}{\left[\frac1n\sum(X_i-\bar X)^2\right]^{2}}-3
$$

**Skewness $b_1$:**
- $b_1>0$: long right tail (mean > median).
- $b_1<0$: long left tail (mean < median).

**Excess kurtosis $b_2$:**
- $b_2>0$: heavier tails / more outliers than the normal.
- $b_2<0$: lighter tails (e.g. uniform).
- Kurtosis is about the **tails**, not the peak.

**For normal data**, $b_1\approx0$ and $b_2\approx0$. Their sampling variability is roughly $\mathrm{Var}(b_1)\approx6/n$ and $\mathrm{Var}(b_2)\approx24/n$.

```r
m  <- mean(x)
m2 <- mean((x - m)^2)
b1 <- mean((x - m)^3) / m2^1.5            # = moments::skewness(x)
b2 <- mean((x - m)^4) / m2^2 - 3          # moments::kurtosis(x) returns b2 + 3 (NOT excess)
```

### 4.4 The tests

**D'Agostino** (skewness) and **Anscombe–Glynn** (kurtosis):
- Each transforms its statistic into $z\sim N(0,1)$ under normality.
- Together they tell you *which* feature fails: asymmetry or tails.

**Jarque–Bera** combines both. Two components give $\chi^2(2)$:

$$JB=\frac n6\left(b_1^2+\frac{b_2^2}{4}\right)\overset{as.}{\sim}\chi^2(2)$$

```r
JB <- length(x)/6 * (b1^2 + b2^2/4)
pchisq(JB, df = 2, lower.tail = FALSE)    # = moments::jarque.test(x)
```

**Shapiro–Wilk:**

$$W=\frac{\left(\sum a_iX_{(i)}\right)^2}{\sum(X_i-\bar X)^2}$$

- **Idea:** compare the ordered data with the values a normal sample "should" have. The coefficients $a_i$ come from expected standard normal order statistics.
- $W$ is essentially the squared correlation of the normal Q-Q plot.
- $W\approx1$: the Q-Q plot is a straight line. **Small $W$ → reject.**
- It is usually the most powerful of these tests.

**Cramér–von Mises and Anderson–Darling** use the *average* squared ECDF gap instead of the maximum. Both are computed from the ordered data with $F_0$ = the fitted normal CDF.

- **CvM** weights the whole range equally:
$$T=\frac1{12n}+\sum_i\left[\frac{2i-1}{2n}-F_0(X_{(i)})\right]^2$$
- **AD** gives extra weight $1/(F_0(1-F_0))$ where $F_0$ is near 0 or 1, so it **sees tail problems**:
$$A=-n-\frac1n\sum_i(2i-1)\left[\ln F_0(X_{(i)})+\ln(1-F_0(X_{(n+1-i)}))\right]$$

```r
z  <- sort(x); n <- length(z); i <- 1:n
F0 <- pnorm(z, mean(x), sd(x))
T_cvm <- 1/(12*n) + sum((F0 - (2*i - 1)/(2*n))^2)            # = nortest::cvm.test(x)$statistic
A_ad  <- -n - mean((2*i - 1) * (log(F0) + log(1 - rev(F0)))) # = nortest::ad.test(x)$statistic
D_lil <- max(pmax(i/n - F0, F0 - (i - 1)/n))                 # = nortest::lillie.test(x)$statistic
```

### 4.5 Lilliefors — and why plain K-S fails after estimation

Lilliefors computes the same $D$ as K-S, but against $\Phi\big((x-\bar X)/S\big)$, with Lilliefors critical values.

**Why:** fitting $\bar X$ and $S$ to the same data moves the curve towards the staircase. $D$ therefore comes out smaller than K-S theory expects, and K-S p-values are far too large. Check it by simulation:

```r
set.seed(1)
pv_ks  <- replicate(2000, { x <- rnorm(50); ks.test(x, "pnorm", mean(x), sd(x))$p.value })
pv_lil <- replicate(2000, { x <- rnorm(50); nortest::lillie.test(x)$p.value })
mean(pv_ks  < 0.05)   # close to 0   -> far below the nominal 5% (test is useless)
mean(pv_lil < 0.05)   # about 0.05   -> correct size
```

### 4.6 Example 3 continued — is the average restart time normal? ($n=246$)

```r
x <- dat$time_avg
moments::agostino.test(x)   # skew = 1.67, z = 8.02, p ~ 1e-15
moments::anscombe.test(x)   # kurt = 8.40 (vs 3), z = 6.08, p ~ 1e-9
moments::jarque.test(x)     # JB = 414, p < 2.2e-16
shapiro.test(x)             # W = 0.888, p ~ 1.7e-12
nortest::cvm.test(x)        # W = 0.675, p ~ 9.5e-8
nortest::ad.test(x)         # A = 4.38, p ~ 6.7e-11
nortest::lillie.test(x)     # D = 0.114, p ~ 3.6e-8
nortest::pearson.test(x)    # P = 49.0, p ~ 3.3e-5
```

**How to read this:**
- Every test rejects normality.
- The skewness and kurtosis tests explain *why*:
  - strong **right skew** ($b_1=1.67$);
  - heavy right tail (kurtosis 8.4, i.e. excess 5.4).
- Check: $JB=\frac{246}6(1.674^2+5.403^2/4)\approx414$ ✔
- Jarque–Bera is the most extreme here because it adds up two huge components.
- Pearson's $\chi^2$ is the "weakest" result, because binning throws away information.

### 4.7 Reading plots like an expert

**Q-Q plot** (data quantiles on the y-axis, model quantiles on the x-axis):

| Pattern | Meaning |
|---|---|
| straight line | model fits |
| right end **above** the line | heavier right tail than the model |
| right end **below** the line | lighter right tail |
| convex "banana" (both ends above) | right skew |
| S-shape, ends pulling away from the line | heavy tails on both sides |
| flat horizontal run | ties / point mass (e.g. many zeros) |

```r
qqnorm(x); qqline(x, col = "red")
hist(x, freq = FALSE); curve(dnorm(x, mean(x), sd(x)), add = TRUE, col = "red")
```

**Cullen–Frey plot** (`fitdistrplus::descdist(x, boot = 1000)`):
- **Axes:** x = squared skewness, y = kurtosis (normal = 3).
- **Reference shapes:**
  - single points: normal (0,3), uniform (0,1.8), logistic (0,4.2), exponential (4,9);
  - lines: gamma and lognormal;
  - region: beta.
- **Data:** the blue dot is your sample; the orange cloud (bootstrap) shows its uncertainty.
- **Use:** pick the candidate families whose point, line or region the cloud covers, then test them formally.

```r
fit <- fitdistrplus::fitdist(x, "gamma")
fitdistrplus::gofstat(fit)   # K-S, CvM, AD statistics for the fitted model
```

Caution: `gofstat` p-values assume known parameters. Treat them as descriptive.

---

## 5. Probability integral transform — the hidden engine

**Claim:** if $X$ is continuous with CDF $F$, then $Y=F(X)\sim\mathrm U(0,1)$.

**Proof:**

$$P(Y\le y)=P(X\le F^{-1}(y))=F(F^{-1}(y))=y$$

```r
x <- rgamma(1e4, shape = 2, rate = 1)        # any continuous distribution
y <- pgamma(x, shape = 2, rate = 1)          # transform with its own CDF
hist(y, breaks = 20)                         # flat -> uniform
ks.test(y, "punif")                          # not rejected
```

**Why it matters:**
- Under $H_0$, $F_0(X_i)$ are uniform whatever $F_0$ is.
- So the null distributions of the K-S, CvM and AD statistics are **the same for every continuous $F_0$**: the tests are distribution-free, and one set of tables serves all models.
- Read backwards, $X=F^{-1}(U)$ is how random number generators produce any distribution.

---

## 6. A complete workflow (copy into a script)

```r
# 1. Look first
summary(x); hist(x, freq = FALSE); boxplot(x, horizontal = TRUE)
plot(ecdf(x)); qqnorm(x); qqline(x)
fitdistrplus::descdist(x, boot = 500)

# 2a. Fully specified model -> K-S (continuous) or chi-square (discrete/binned)
ks.test(x, "pexp", rate = 0.04)

# 2b. Normal with unknown parameters
shapiro.test(x); nortest::ad.test(x); nortest::lillie.test(x)

# 2c. Other family with estimated parameters -> chi-square with df = k - 1 - m
br <- c(0, quantile(x, c(.2, .4, .6, .8)), Inf)
N  <- table(cut(x, br, include.lowest = TRUE))
p  <- diff(pexp(br, rate = 1/mean(x)))
E  <- length(x) * p
K  <- sum((N - E)^2 / E)
pchisq(K, df = length(N) - 1 - 1, lower.tail = FALSE)

# 3. Report: statistic, df, p-value, decision, and what the plots show
```

> [!note] On data-driven bins in step 2c
> Using quantiles of the data as cut points makes the bins data-driven. That is fine in practice and keeps the expected counts roughly balanced.

---

## 7. Common mistakes

> [!failure] Avoid these
> 1. Forgetting the **$-m$** in the df: `chisq.test` won't do it for you.
> 2. Checking the "≥ 5" rule on **observed** counts instead of **expected** counts.
> 3. Bins that **don't cover the whole support** ($\sum p_j<1$).
> 4. Running `ks.test(x, "pnorm", mean(x), sd(x))`: estimated parameters make the test invalid → use Lilliefors.
> 5. Using K-S on **discrete** data or data with many ties.
> 6. Reading a large p-value as "the data are normal". It only means "no evidence against normality".
> 7. Trusting tests alone with huge $n$ (everything rejects) or tiny $n$ (nothing rejects). **Use plots.**
> 8. `moments::kurtosis()` returns kurtosis **including** the 3. Subtract 3 for the excess.

---

## 8. Practice problems (click to reveal)

> [!question]- P1. A die is rolled 60 times: counts 8, 12, 9, 11, 6, 14. Is it fair? Do it by hand and in R.
> - Expected counts: $n_j=10$ for every face.
> - $K=(4+4+1+1+16+16)/10=4.2$.
> - $df=5$; critical value 11.07; $p\approx0.52$.
> - **Not rejected.**
> ```r
> chisq.test(c(8,12,9,11,6,14))   # p defaults to equal probabilities
> ```

> [!question]- P2. Counts of 0,1,2,3,≥4 accidents per day over 100 days: 35, 38, 18, 7, 2. The total number of accidents was 105. Test a Poisson fit.
> 1. Estimate: $\hat\lambda=105/100=1.05$, so $m=1$.
> 2. Probabilities: $p=(e^{-\lambda},\lambda e^{-\lambda},\dots)$ for 0–3, and $P(X\ge4)$ for the last cell.
> 3. Expected counts: $\approx(35.0, 36.7, 19.3, 6.7, 2.2)$.
> 4. The last cell has $n_j<5$, so merge "3" and "≥4" → $N=(35,38,18,9)$, $k=4$.
> 5. $df=4-1-1=2$.
> ```r
> lam <- 1.05
> p   <- c(dpois(0:2, lam), ppois(2, lam, lower.tail = FALSE))
> N   <- c(35, 38, 18, 9); E <- 100 * p
> K   <- sum((N - E)^2 / E)                       # about 0.15
> pchisq(K, df = 4 - 1 - 1, lower.tail = FALSE)   # large p -> Poisson not rejected
> ```

> [!question]- P3. Why does R's `chisq.test` give the wrong p-value in Example 2?
> It uses $df=k-1=7$, but $\lambda$ was estimated, so the correct df is $6$. Compute the p-value with `pchisq(K, 6, lower.tail = FALSE)`.

> [!question]- P4. $n=100$, one-sample K-S, $D=0.15$. Decide at 5% and at 1%.
> - $\sqrt n D=1.5$.
> - At 5%: $1.5>1.36$ → **reject**.
> - At 1%: $1.5<1.63$ → **do not reject**.
> - The p-value is $\approx2e^{-2\cdot2.25}\approx0.022$.

> [!question]- P5. Simulate 30 values from $t_3$. Which normality tests detect it? Repeat with $n=300$.
> ```r
> set.seed(3); x <- rt(30, df = 3)
> shapiro.test(x); nortest::ad.test(x); nortest::lillie.test(x)
> qqnorm(x); qqline(x)   # S-shape: heavy tails
> ```
> - With $n=30$, the tests often fail to reject (low power).
> - With $n=300$, they reject reliably.
> - Lilliefors is typically the weakest of the three.

> [!question]- P6. Compute $D$ for the sample $\{0.1, 0.4, 0.7\}$ under $H_0:\mathrm U(0,1)$ by hand.
>
> | $i$ | $X_{(i)}$ | $F_0=X_{(i)}$ | $i/n-F_0$ | $F_0-(i-1)/n$ |
> |---|---|---|---|---|
> | 1 | 0.1 | 0.1 | 0.233 | 0.1 |
> | 2 | 0.4 | 0.4 | 0.267 | 0.067 |
> | 3 | 0.7 | 0.7 | 0.3 | 0.033 |
>
> The largest entry is $D=0.3$.

> [!question]- P7. Show with a simulation that $F(X)$ is uniform when $X$ is lognormal.
> ```r
> x <- rlnorm(5000, meanlog = 1, sdlog = 1.1)
> hist(plnorm(x, 1, 1.1))
> ```
> The histogram is flat.

> [!question]- P8. Shapiro–Wilk gives p = 0.003 with n = 2000, but the Q-Q plot is almost a straight line. What do you conclude?
> - With such a large $n$, tiny deviations become significant.
> - If the Q-Q plot shows no meaningful departure, methods that are robust to mild non-normality (e.g. t-tests with large $n$) are still fine.
> - Report both the test result and the plot.

---

## 9. Corrections to the slides

> [!warning] Use these corrected versions
> 1. **Slide 19:** the test uses $\lambda=0.04$ (the title says 0.1).
> 2. **K-S critical value:** $\sqrt n D\ge\sqrt{\tfrac12\ln(2/\alpha)}$, which is 1.36 for $\alpha=0.05$.
> 3. **Two-sample K-S:** scale $D$ by $\sqrt{nm/(n+m)}$, not $\sqrt n$.
> 4. **Variance of the excess kurtosis:** it has a factor $n$ in the numerator, so it is $\approx24/n$.
> 5. **CvM statistic:** the term is $\frac{2i-1}{2n}$, not $\frac{2i-1}{n}$.