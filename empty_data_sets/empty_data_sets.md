empty\_data\_sets
================
JP
July 9, 2017

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

``` r
# empty data frame with default names
data.frame(matrix(nrow = 10, ncol = 4)) %>% as.tibble()
```

    ## # A tibble: 10 x 4
    ##       X1    X2    X3    X4
    ##    <lgl> <lgl> <lgl> <lgl>
    ##  1    NA    NA    NA    NA
    ##  2    NA    NA    NA    NA
    ##  3    NA    NA    NA    NA
    ##  4    NA    NA    NA    NA
    ##  5    NA    NA    NA    NA
    ##  6    NA    NA    NA    NA
    ##  7    NA    NA    NA    NA
    ##  8    NA    NA    NA    NA
    ##  9    NA    NA    NA    NA
    ## 10    NA    NA    NA    NA

``` r
# empty data frame with assigned names
data.frame(matrix(nrow = 10, ncol = 4)) %>% setNames(., c("dv", "N1", "MN1", "SD1")) %>% as.tibble()
```

    ## # A tibble: 10 x 4
    ##       dv    N1   MN1   SD1
    ##    <lgl> <lgl> <lgl> <lgl>
    ##  1    NA    NA    NA    NA
    ##  2    NA    NA    NA    NA
    ##  3    NA    NA    NA    NA
    ##  4    NA    NA    NA    NA
    ##  5    NA    NA    NA    NA
    ##  6    NA    NA    NA    NA
    ##  7    NA    NA    NA    NA
    ##  8    NA    NA    NA    NA
    ##  9    NA    NA    NA    NA
    ## 10    NA    NA    NA    NA

``` r
empty <- tibble(
  temp = 1:10,
  X1  = NA,
  X2 = NA
) %>% 
  select(-temp)

stocks <- tibble(
  year   = c(2015, 2015, 2016, 2016),
  half  = c(   1,    2,     1,    2),
  return = c(1.88, 0.59, 0.92, 0.17)
)

tibble(
  x = 1:5, 
  y = 1, 
  z = x ^ 2 + y
)
```

    ## # A tibble: 5 x 3
    ##       x     y     z
    ##   <int> <dbl> <dbl>
    ## 1     1     1     2
    ## 2     2     1     5
    ## 3     3     1    10
    ## 4     4     1    17
    ## 5     5     1    26

"`tribble()` is short for *tr*ansposed tibble. `tribble()` is customised for data entry in code: column headings are defined by formulas (i.e. they start with ~), and entries are separated by commas. This makes it possible to lay out small amounts of data in easy to read form" ([R4DS](http://r4ds.had.co.nz/tibbles.html)).

``` r
mydata <- tribble(
  ~group_a, ~group_b, ~group_c, ~group_d, 
  21,       14,       10,       23,
  24,       20,       12,       35,
  34,       45,       34,       14
)
```
