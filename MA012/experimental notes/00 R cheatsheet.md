---
tags: [ma012, moc, type/R]
aliases: [R cheatsheet, R functions]
---

# R cheatsheet — MA012

Packages used: `tidyverse`, `car`, `agricolae`, `broom`, `moments`, `nortest`, `fitdistrplus`, `GGally`, `ggcorrplot`, `ppcor`.

```r
install.packages(c("tidyverse", "car", "agricolae", "broom", "moments", "nortest",
                   "fitdistrplus", "GGally", "ggcorrplot", "ppcor", "palmerpenguins"))
```

## Distributions → [[Random variable and distribution]]
```r
qnorm(0.975); qt(0.975, df); qchisq(0.95, df); qf(0.95, d1, d2); qtukey(0.95, k, df)
pt(-abs(T), df) * 2                    # two-sided t p-value
pchisq(K, df, lower.tail = FALSE)      # right-tail chi2 p-value
pf(F, d1, d2, lower.tail = FALSE)      # right-tail F p-value
dbinom / pbinom; dsignrank / psignrank; dwilcox / pwilcox   # exact rank distributions
```

## Description and plots → [[Descriptive statistics and EDA]]
```r
dat |> group_by(g) |> summarise(n = n(), mean = mean(y), sd = sd(y), median = median(y))
ggplot(dat, aes(g, y)) + geom_boxplot() + geom_jitter(width = 0.2)
ggplot(dat, aes(sample = y)) + geom_qq() + geom_qq_line()   # Q-Q plot
plot(ecdf(x))                                               # ECDF
```

## t-tests → [[t-tests]]
```r
t.test(x, mu = 60)                          # one-sample
t.test(y ~ g, data = dat)                   # Welch (default)
t.test(y ~ g, data = dat, var.equal = TRUE) # pooled
t.test(after, before, paired = TRUE)        # paired
```

## ANOVA → [[One-way ANOVA]], [[Two-way ANOVA]]
```r
m <- aov(y ~ A, data = dat); summary(m)
model.tables(m, "means"); model.tables(m, "effects"); coef(m)
anova(lm(y ~ 1, dat), lm(y ~ A, dat))              # model vs submodel
TukeyHSD(m); agricolae::HSD.test(m, "A", group = TRUE)$groups
agricolae::LSD.test(m, "A", p.adj = "bonferroni"); agricolae::scheffe.test(m, "A")
pairwise.t.test(dat$y, dat$A, p.adjust.method = "bonferroni")
# assumptions
r <- residuals(m); shapiro.test(r); qqnorm(r); qqline(r)
car::leveneTest(y ~ A, data = dat); bartlett.test(y ~ A, data = dat)
oneway.test(y ~ A, data = dat, var.equal = FALSE)  # Welch ANOVA
# two-way
m2 <- aov(y ~ A * B, data = dat); car::Anova(lm(y ~ A * B, dat), type = 2)
```

## Rank and permutation tests → [[L03 Rank tests]]
```r
rank(x); sort(x); order(x)
binom.test(sum(x > x0), sum(x != x0))               # sign test
wilcox.test(x, mu = x0)                             # signed-rank (V = T+)
wilcox.test(y ~ g, data = dat)                      # rank-sum (W = U1)
kruskal.test(y ~ g, data = dat); agricolae::kruskal(dat$y, dat$g)
pairwise.wilcox.test(dat$y, dat$g, p.adjust.method = "bonferroni")
agricolae::Median.test(dat$y, dat$g); agricolae::waerden.test(dat$y, dat$g)
```

## Goodness of fit and normality → [[L04 Goodness-of-fit tests]]
```r
chisq.test(N, p = p)                                # df = k-1 only!
pchisq(K, df = k - 1 - m, lower.tail = FALSE)       # with m estimated parameters
ks.test(x, "pexp", rate = 0.04); ks.test(x, y)
shapiro.test(x); nortest::lillie.test(x); nortest::ad.test(x); nortest::cvm.test(x)
nortest::pearson.test(x)
moments::agostino.test(x); moments::anscombe.test(x); moments::jarque.test(x)
moments::skewness(x); moments::kurtosis(x)          # kurtosis includes the 3
fitdistrplus::descdist(x, boot = 1000)              # Cullen-Frey
```

## Correlation → [[L05 Correlation analysis]]
```r
cov(x, y); cor(x, y); cor.test(x, y)                # Pearson
cor.test(x, y, method = "spearman"); cor.test(x, y, method = "kendall")
cov(Z); cor(Z); GGally::ggpairs(Z); ggcorrplot::ggcorrplot(cor(Z), lab = TRUE)
ppcor::pcor(Z)                                      # partial correlations + p-values
atanh(r); tanh(atanh(r) + c(-1, 1) * qnorm(0.975) / sqrt(n - 3))   # Fisher z CI
```
