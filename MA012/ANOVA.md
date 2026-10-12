comparing means using variance XD
##### familiwise error rate 
- probability of at least one Type I error: $\alpha_{FW}=1-(1-\alpha)^s$ where $s$  is number of successive tests (sequence). 
## One way ANOVA
- observations denoted by $Y_{1,1},...,Y_{1,n}$ 
- sample size of $n_i$
- sample mean $Y(hat)_i.$ (that dot means to compute sample mean from all samples in group)
- ### Assumptions
	- **we assume same variance and $~N(\mu_i, \sigma^2)$**
	- **we assume observations are mutally independent**
	- we assume that random errors follow normal probability distribution $e_{ij} \~ N(\mu_i, \rho^2)$
	- **we assume homogeneous variance $\sigma^2$ - the same for all observations in all groups**
- we need identification constrainst to indentify unique solution among infinitely many
- check proof in presentation slide 8
### normallity of data
we can check normality of data with:
	- **Shapiro-wilk test** (3<=n<=5000)
	- graphically: Q-Q plot
	- **lilliefor's test (n>=100, weak power)**, Anderson-Darling test(n>=50)
### Homogenity of variance
we can check homogeity of variance in data with:
- **Levene’s test** (leveneTest): standard widely used test, highly robust to outliers(n>=5)
-  **Bartlett’s test** (bartlett.test): requires precise normality, not robust to outliers, but it has high power
-  graphically: boxplots, residuals vs. fitted values plot
### Post-hoc test
When and **ONLY** when we ruled out $H_0$ we can use post-hoc tests to know which groups have variant mean. 
- **Tukey's Honest Significant Difference** (HSD)
- **Scheffe's test**


TODO:
- [x] review type of errors
- [ ] review tests and their derivation
- [ ] review p value, confidence intervals
- [x] add post hoc tests 
- [ ] review distributions:
	- + chi square
	- fisher -  snedicotr 
	- others
- 
- Fisher 1918: variance as useful statistical tool  , 1921: ANOVA, book 1925
- [ ] after learning anova, use summary as checklist, one you understand everything, you can move to the rest of presentation not mandatory... -> it is for you