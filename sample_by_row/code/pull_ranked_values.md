From several columns pull a ranked subset of values by row
================
JP
September 6, 2017

``` r
rm(list = ls())

library(knitr)
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
library(readr)

file_rank_data <- "../data/rank_data.csv"
```

``` r
# Note that some data will have 4 rows per id because of ties in rank. 
d <- read_csv(file_rank_data) %>% 
  gather(a1_rating:a6_rating, key = a, value = rating) %>% 
  group_by(id) %>%
  mutate(abs_rank = abs(rating - subject_rating) %>% min_rank()) %>%
  arrange(abs_rank) %>% 
  slice(1:3) %>% 
  # filter(abs_rank <= 3) # if you can handle >3 values due to rank ties
  arrange(desc(id))
```

    ## Parsed with column specification:
    ## cols(
    ##   id = col_integer(),
    ##   subject_rating = col_integer(),
    ##   a1_rating = col_integer(),
    ##   a2_rating = col_integer(),
    ##   a3_rating = col_integer(),
    ##   a4_rating = col_integer(),
    ##   a5_rating = col_integer(),
    ##   a6_rating = col_integer()
    ## )

``` r
d %>% kable()
```

|   id|  subject\_rating| a          |  rating|  abs\_rank|
|----:|----------------:|:-----------|-------:|----------:|
|    5|                5| a6\_rating |       5|          1|
|    5|                5| a2\_rating |       4|          2|
|    5|                5| a3\_rating |       8|          3|
|    4|                5| a3\_rating |       6|          1|
|    4|                5| a6\_rating |       6|          1|
|    4|                5| a2\_rating |       8|          3|
|    3|                5| a2\_rating |       4|          1|
|    3|                5| a4\_rating |       2|          2|
|    3|                5| a6\_rating |       8|          2|
|    2|                7| a6\_rating |       6|          1|
|    2|                7| a1\_rating |       9|          2|
|    2|                7| a3\_rating |       9|          2|
|    1|                6| a4\_rating |       5|          1|
|    1|                6| a6\_rating |       7|          1|
|    1|                6| a1\_rating |       4|          3|

optional code to return data to wide format
===========================================

``` r
d %>% 
  select(-abs_rank) %>%
  spread(key = a, value = rating)
```

    ## # A tibble: 5 x 7
    ## # Groups:   id [5]
    ##      id subject_rating a1_rating a2_rating a3_rating a4_rating a6_rating
    ## * <int>          <int>     <int>     <int>     <int>     <int>     <int>
    ## 1     1              6         4        NA        NA         5         7
    ## 2     2              7         9        NA         9        NA         6
    ## 3     3              5        NA         4        NA         2         8
    ## 4     4              5        NA         8         6        NA         6
    ## 5     5              5        NA         4         8        NA         5

Longer solution using list columns
==================================

``` r
ds <- read_csv(file_rank_data) %>% 
  rowwise() %>% 
  mutate(
    rating_list = list(c(a1_rating, a2_rating, a3_rating, a4_rating, 
      a5_rating, a6_rating)),
    rating_centered = list(rating_list - subject_rating),
    rating_centered_ranked = list(min_rank(abs(rating_centered)))
  ) %>% 
  ungroup() %>% 
  unnest() 
```

    ## Parsed with column specification:
    ## cols(
    ##   id = col_integer(),
    ##   subject_rating = col_integer(),
    ##   a1_rating = col_integer(),
    ##   a2_rating = col_integer(),
    ##   a3_rating = col_integer(),
    ##   a4_rating = col_integer(),
    ##   a5_rating = col_integer(),
    ##   a6_rating = col_integer()
    ## )

``` r
ds %>%  
  group_by(id) %>% 
  arrange(rating_centered_ranked) %>% 
  slice(1:3)
```

    ## # A tibble: 15 x 11
    ## # Groups:   id [5]
    ##       id subject_rating a1_rating a2_rating a3_rating a4_rating a5_rating
    ##    <int>          <int>     <int>     <int>     <int>     <int>     <int>
    ##  1     1              6         4         3         9         5         9
    ##  2     1              6         4         3         9         5         9
    ##  3     1              6         4         3         9         5         9
    ##  4     2              7         9         3         9         1         5
    ##  5     2              7         9         3         9         1         5
    ##  6     2              7         9         3         9         1         5
    ##  7     3              5         9         4         9         2         9
    ##  8     3              5         9         4         9         2         9
    ##  9     3              5         9         4         9         2         9
    ## 10     4              5         9         8         6         9         8
    ## 11     4              5         9         8         6         9         8
    ## 12     4              5         9         8         6         9         8
    ## 13     5              5         9         4         8         8         9
    ## 14     5              5         9         4         8         8         9
    ## 15     5              5         9         4         8         8         9
    ## # ... with 4 more variables: a6_rating <int>, rating_list <int>,
    ## #   rating_centered <int>, rating_centered_ranked <int>
