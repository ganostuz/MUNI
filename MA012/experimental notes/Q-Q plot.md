---
tags: [ma012, lecture/L01, lecture/L04, type/concept]
aliases: [QQ plot, Quantile-quantile plot]
lecture: [L01, L02, L04]
---

# Q-Q plot

**Idea.** Plot the sorted data (sample quantiles) against the quantiles a model distribution says they *should* have.
- If the model is right, the points lie on a **straight line**.
- For a normal Q-Q plot the line has intercept $\approx\mu$ and slope $\approx\sigma$, so you don't need to know the parameters.

```r
qqnorm(x); qqline(x, col = "red")                    # base R
ggplot(dat, aes(sample = x)) + geom_qq() + geom_qq_line()   # ggplot2
# against another distribution, e.g. Ex(0.04):
qqplot(qexp(ppoints(length(x)), rate = 0.04), sort(x)); abline(0, 1)
```

## Reading the shape (sample quantiles on the y-axis)
| Pattern | Meaning |
|---|---|
| straight line | model fits |
| convex "banana" (both ends above the line) | right skew |
| concave (both ends below) | left skew |
| S-shape: left end below, right end above | heavy tails on both sides (e.g. $t$) |
| inverted S | light tails (e.g. uniform) |
| right end below the line | lighter right tail than the model |
| flat horizontal steps | ties or a point mass (e.g. many zeros) |

In [[One-way ANOVA]], draw the Q-Q plot of the **residuals** (pooled and per group). See [[ANOVA assumptions and diagnostics]].

> [!tip] Large samples
> With large $n$, formal tests reject even trivial deviations, so trust the Q-Q plot to judge practical importance. See [[Normality tests]].

Related: [[Shapiro-Wilk test]] (its $W$ ≈ the straightness of this plot) · [[Skewness and kurtosis]]
