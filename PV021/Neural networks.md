Machine learning - construction of systems that learn their functionality from data...
`read attention is all you need`
look into neuroscience


# statistics
just explaining new syntax `|>` and `%>%` is pipe in R. We should write code using it and not many paratheses. wrong: `(((((((...)))))))` good: `|> |> |> |> |> ...` it inserts argument as first argument of following function. Functions to learn: 
```{r}
str(tab)
tab$sex
tab |> pull(Sex)
glimpse(tab)
head(tab)
names(tab)
dim(tab)
kable(tab)
tab |> slice_head(n=10)
summary(tab)
group_by()

```
repeat basic statistical values and their calculation. **factor** in r is sign to let r treat it as nominal data. ordinal.  When using nominal data denoted by **factor** there are unique value they are named **levels** in r show them using `levels(tab)` . *R starts indexing at **1** wtf.* Pozri `as.numeric() vs is.numeric()` . **Look at functions in R.** Nearly all the function automatically work for multi dim arrays, typically it will just break it into elemt by elemnt basis, hence  it is often enough to just write function for one number. Look at lambdas (do not use them though xd). 