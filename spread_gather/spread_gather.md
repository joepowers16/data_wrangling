spread and gather
================
JP
Aug 10, 2017

``` r
library(tidyverse)
```

    ## Loading tidyverse: ggplot2
    ## Loading tidyverse: tibble
    ## Loading tidyverse: tidyr
    ## Loading tidyverse: readr
    ## Loading tidyverse: purrr
    ## Loading tidyverse: dplyr

    ## Conflicts with tidy packages ----------------------------------------------

    ## filter(): dplyr, stats
    ## lag():    dplyr, stats

gather and spread examples
--------------------------

``` r
# create some data to spread and gather
mydata <- tribble(
  ~group_a, ~group_b, ~group_c, ~group_d, 
  21,       14,       10,       23,
  24,       20,       12,       35,
  34,       45,       34,       14
)

mydata
```

    ## # A tibble: 3 x 4
    ##   group_a group_b group_c group_d
    ##     <dbl>   <dbl>   <dbl>   <dbl>
    ## 1      21      14      10      23
    ## 2      24      20      12      35
    ## 3      34      45      34      14

``` r
# Tidy up the data
mydata_gathered <- 
  mydata %>% 
  mutate(row = row_number()) %>% # rownumber is key for later spread b/c you
  # need a variable that uniquely identifyies observations
  gather(
    group_a:group_d, # old variables to gather
    key = group,     # new variable whose values will be old variable names
    value = score    # new variable whose values will be old variable values
  ) 

mydata_gathered
```

    ## # A tibble: 12 x 3
    ##      row   group score
    ##    <int>   <chr> <dbl>
    ##  1     1 group_a    21
    ##  2     2 group_a    24
    ##  3     3 group_a    34
    ##  4     1 group_b    14
    ##  5     2 group_b    20
    ##  6     3 group_b    45
    ##  7     1 group_c    10
    ##  8     2 group_c    12
    ##  9     3 group_c    34
    ## 10     1 group_d    23
    ## 11     2 group_d    35
    ## 12     3 group_d    14

``` r
# demo a spread
mydata_spread <- 
  mydata_gathered %>% 
  spread(key = group, value = score)

mydata_spread
```

    ## # A tibble: 3 x 5
    ##     row group_a group_b group_c group_d
    ## * <int>   <dbl>   <dbl>   <dbl>   <dbl>
    ## 1     1      21      14      10      23
    ## 2     2      24      20      12      35
    ## 3     3      34      45      34      14

"row number `spread()`" soln from [link](https://stackoverflow.com/questions/25960394/spread-with-data-frame-tibble-with-duplicate-identifiers).

`convert = TRUE` will produce variables of mixed types for `spread()` and detect types for `gather()`.

``` r
stocks <- tibble(
  year   = c(2015, 2015, 2016, 2016),
  half  = c(   1,    2,     1,    2),
  return = c(1.88, 0.59, 0.92, 0.17)
)

stocks %>% 
  spread(
    key = year, 
    value = return) 
```

    ## # A tibble: 2 x 3
    ##    half `2015` `2016`
    ## * <dbl>  <dbl>  <dbl>
    ## 1     1   1.88   0.92
    ## 2     2   0.59   0.17

``` r
stocks %>% 
  spread(key = year, value = return) %>% 
  gather(key = year, value = return, `2015`:`2016`, convert = TRUE) %>% 
  select(year, half, return)
```

    ## # A tibble: 4 x 3
    ##    year  half return
    ##   <int> <dbl>  <dbl>
    ## 1  2015     1   1.88
    ## 2  2015     2   0.59
    ## 3  2016     1   0.92
    ## 4  2016     2   0.17

From R4DS 12.3.3 Why are `gather()` and `spread()` not perfectly symmetrical?

``` r
stocks <- tibble(
  year   = c(2015, 2015, 2016, 2016),
  half  = c(   1,    2,     1,    2),
  return = c(1.88, 0.59, 0.92, 0.17)
)

stocks %>% 
  spread(year, return) %>% 
  gather("year", "return", `2015`:`2016`)
```

    ## # A tibble: 4 x 3
    ##    half  year return
    ##   <dbl> <chr>  <dbl>
    ## 1     1  2015   1.88
    ## 2     2  2015   0.59
    ## 3     1  2016   0.92
    ## 4     2  2016   0.17

Gathered variables default to character class, and do not return to original class when re-spread.

Both spread() and gather() have a convert argument.

``` r
stocks %>% 
  spread(year, return, convert = TRUE) 
```

    ## # A tibble: 2 x 3
    ##    half `2015` `2016`
    ## * <dbl>  <dbl>  <dbl>
    ## 1     1   1.88   0.92
    ## 2     2   0.59   0.17

``` r
stocks %>% 
  spread(year, return) %>% 
  gather("year", "return", `2015`:`2016`, convert = TRUE) 
```

    ## # A tibble: 4 x 3
    ##    half  year return
    ##   <dbl> <int>  <dbl>
    ## 1     1  2015   1.88
    ## 2     2  2015   0.59
    ## 3     1  2016   0.92
    ## 4     2  2016   0.17

Code below fails because variables with numeric names require back ticks, e.g., `1999`.

``` r
table4a %>% 
  gather(1999, 2000, key = "year", value = "cases")
```

The `spread()` fails in the tibble below because of duplicate identifiers in the `names` column. This is because Phillip Woods has two ages listed.
- Adding row numbers will fix the problem, but create a long and sparse dataset.
- Resolving the age double entry for Woods is a better soln.

``` r
people <- tribble(
  ~name,             ~key,    ~value,
  #-----------------|--------|------
  "Phillip Woods",   "age",       45,
  "Phillip Woods",   "height",   186,
  "Phillip Woods",   "age",       50,
  "Jessica Cordero", "age",       37,
  "Jessica Cordero", "height",   156
)
```

``` r
people %>% spread(key = key, value = value)
```

``` r
people %>% 
  mutate(row_num = seq(1:nrow(.))) %>% 
  spread(key = key, value = value)
```

    ## # A tibble: 5 x 4
    ##              name row_num   age height
    ## *           <chr>   <int> <dbl>  <dbl>
    ## 1 Jessica Cordero       4    37     NA
    ## 2 Jessica Cordero       5    NA    156
    ## 3   Phillip Woods       1    45     NA
    ## 4   Phillip Woods       2    NA    186
    ## 5   Phillip Woods       3    50     NA

``` r
people %>% 
  mutate(
    value = ifelse(name == "Phillip Woods" & key == "age", 47.5, value)
  ) %>% 
  filter(!duplicated(.)) %>% 
  spread(key = key, value = value)
```

    ## # A tibble: 2 x 3
    ##              name   age height
    ## *           <chr> <dbl>  <dbl>
    ## 1 Jessica Cordero  37.0    156
    ## 2   Phillip Woods  47.5    186

Tidy the simple tibble below. Do you need to spread or gather it? What are the variables?

``` r
preg <- tribble(
  ~pregnant, ~male, ~female,
  "yes",     NA,    10,
  "no",      20,    12
)

preg %>% 
  gather(male, female, key = gender, value = number) %>% 
  select(gender, pregnant, number)
```

    ## # A tibble: 4 x 3
    ##   gender pregnant number
    ##    <chr>    <chr>  <dbl>
    ## 1   male      yes     NA
    ## 2   male       no     20
    ## 3 female      yes     10
    ## 4 female       no     12

These data can be tidied via a gather and the new variables are gender, pregnant, and number.
