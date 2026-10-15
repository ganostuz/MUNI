---
tags: [ma012, lecture/L05, type/concept]
aliases: [Correlation and independence, Anscombe's quartet, Datasaurus, Simpson's paradox]
lecture: L05
---

# Correlation pitfalls

## 1. Correlation and independence
- Independent $X,Y$ ⇒ uncorrelated ($\rho=0$).
- **But** uncorrelated does **not** imply independent.
  - Example: $Y=X^2$ with $X$ symmetric gives $\rho=0$, yet $Y$ is a function of $X$.
  - Panels A–L on slide 8: parabolas and circles give $r\approx0$, while some nonlinear patterns still give large $r$.
- **For a bivariate normal distribution**, uncorrelated ⇔ independent → [[Bivariate normal distribution]].

## 2. Anscombe's quartet (1973)
- **Four datasets** with identical $n=11$, $\bar X=9$, $\bar Y=7.5$, $S_X=\sqrt{11}$, $S_Y\approx2.03$ and $r\approx0.816$.
- **Completely different pictures:**
  - a linear cloud;
  - a curve;
  - a line with one outlier;
  - a vertical stack with one leverage point.

**Lesson:** always plot. `datasets::anscombe`.

## 3. Datasaurus dozen (Matejka & Fitzmaurice, 2017)
- **13 datasets** (including a dinosaur, a star and circles) with nearly identical means, SDs and $r\approx-0.06$.
- `datasauRus::datasaurus_dozen`.

## 4. Simpson's paradox (1951)
A trend that appears in every group disappears or **reverses** when the groups are pooled.

**Palmer penguins example:** bill length vs bill depth.

| Group | $r$ |
|---|---|
| Adelie | +0.39 |
| Chinstrap | +0.65 |
| Gentoo | +0.64 |
| **all species pooled** | **−0.24** |

**Cause:** species is a confounder. Handle it by stratifying, or use [[Partial correlation]].

```r
library(palmerpenguins)
penguins |> group_by(species) |>
  summarise(r = cor(bill_length_mm, bill_depth_mm, use = "complete.obs"))
cor(penguins$bill_length_mm, penguins$bill_depth_mm, use = "complete.obs")
```

## 5. Correlation ≠ causation
A strong $r$ can be **spurious** (a common cause) or **mediated**. See the Pearson vs partial table in [[Partial correlation]].

Related: [[Pearson correlation]] · [[Descriptive statistics and EDA]]
