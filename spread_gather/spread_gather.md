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
    value = score    # new variable whose valies will be old variable values
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
