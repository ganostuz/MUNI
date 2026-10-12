We have multiple data types among them are:
# By variable type
## Discrete
- can be listed
- takes on finitely or countably many values, often integers
- e. g., gender, blood type, grades, competition ranking, number of children
## Continous
- uncountably many values, typically from an interval, arbitrarily precise
- for numeric, i. e., interval and ratio data
- e. g., height, weight, time
# Categorical:
## Nominal data
- defined operations: =, ≠, set membership (classification);
- no ordering and comparison;
- categorical data, discrete; in R: **factor**;
- e. g., blood type, gender, region;
- dichotomous data: 2 categories, often FALSE / TRUE;
- dummy variable: numeric value 1 / 0 encodes membership to a specific
category.
## Ordinal data
- additionally defined: order, rank;
- additional operations: <, >, comparison, sorting;
- categorical data, discrete; in R: **ordered factor**;
- distance between values cannot be quantified;
- e. g., the highest level of education attained, grades, competition ranking.
# Numerical
## Interval data
- additionally defined: distance;
- additional operations: +, −;
- typically continuous numerical data, in R: **numeric, integer**;
- ratio of values cannot be correctly quantified, zero is not fixed;
- e. g., temperature in ∘C, calendar year.
## Ration data
- additionally defined: ratio;
- additional operations: ∗, /;
- position of zero and scale are fixed;
- typically continuous numerical data, in R: **numeric, integer**;
- physical quantities defined by SI units, e.g., thermodynamic temperature
in K, mass, length, duration.
- The meaning of the data (particular variables, the experiment,
preprocessing, etc.) is needed.
- Continuous numerical data can be treated (with a certain loss of
information) as discrete data: the data are classified into intervals, which
play the role of (ordered) categories.