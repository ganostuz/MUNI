
## Functions
- `<-` is used to assign something to something. Often times it is result of function or constant to variable.
- `library(tinyverse)` is used to load library named *tinyverse"
- ```read.csv("people.csv")``` reads csv 
- `as_tibble(read_csv_var)` every row is observation and every column is atribute, converts into tibble
- `|>` or `%>%` is used as pipe in bash
- `slice_head(tab ,n, prop)` is used as regular head command and will output `n` **lines** from table tab or `prop` **proportion** rows of table.
- `head(tab)` will take first 10 entries and displays it in simple table like `kable()` with some additional information like datatype *should not be used often in course*
- `kable(tab)` is easy way to plot `tab` into simple table
- `names(tab)` gives us attribute names of `tab`
- `dim(tab)` gives us dimensions of `tab` in format: `rows colls`
- `glimpse(tab)` gives us glimpse at the data: rows are attributes columns are indexes and cells are data. This also shows data type of attributes.
- `summary(tab)` gives us summary for every attribute. Basic statistical values based on data type of attribute.
- `levels(tab$gender)` shows us possible values for gender attribute
- `as.numeric(tab$gender)` shows us all values of `tab$gender` in numeric format
- funcition definition syntax:
- ```{r}
# Classical syntax

my_fun <- function(x) {
  x^2
}

my_fun(2)
  ```
- sequences generation:
	- `s <- seq(1,10,by=1)` -> `1 2 3 4 5 6 7 8 9` 10
	- `1:10` -> `1 2 3 4 5 6 7 8 9 10`
## Descriptive statistics
- we can compute statistics like this in summarise:
```{r}
  tab2 %>%
  summarise(
    Height.mean = mean(Height),
    Weight.mean = mean(Weight)
  )
```
- `group_by(tab, Attribute)` splits tab based on attribute, can be piped into `summarise`
- `c(1,2,3,4,5)` vector creation, arguments must be of same type
- `list(1, "word")` list creation, do not have to be of same type can even do `list(a=mean(a), b=mean(b))`
- 
## Snippets i do not understand (fully ) yet:
```{r}
tab2 <- tab %>%
  mutate(
    Gender = factor(Gender),
    Driver = factor(Driver)
  )

glimpse(tab2)
```

```{r}
tab2 <- tab %>%
  mutate(
    across(
      where(is.character),
      factor
    )
  )

glimpse(tab2)
```

## Observations
- **always use tibble, never data.frame** 
- **indexing is from 1**