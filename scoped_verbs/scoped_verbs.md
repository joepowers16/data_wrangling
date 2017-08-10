scoped verbs
================
JP
August 8, 2017

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

The single table verbs all come in three scoped variants: `_all`, `_at`, & `_if`

`_all()` behaves like a `map()` function.

``` r
summarise_all(mtcars, mean)
```

    ##        mpg    cyl     disp       hp     drat      wt     qsec     vs
    ## 1 20.09062 6.1875 230.7219 146.6875 3.596563 3.21725 17.84875 0.4375
    ##        am   gear   carb
    ## 1 0.40625 3.6875 2.8125

``` r
map_dbl(mtcars, mean)
```

    ##        mpg        cyl       disp         hp       drat         wt 
    ##  20.090625   6.187500 230.721875 146.687500   3.596563   3.217250 
    ##       qsec         vs         am       gear       carb 
    ##  17.848750   0.437500   0.406250   3.687500   2.812500

``` r
summarise_all(mtcars, funs(mean, sd))
```

    ##   mpg_mean cyl_mean disp_mean  hp_mean drat_mean wt_mean qsec_mean vs_mean
    ## 1 20.09062   6.1875  230.7219 146.6875  3.596563 3.21725  17.84875  0.4375
    ##   am_mean gear_mean carb_mean   mpg_sd   cyl_sd  disp_sd    hp_sd
    ## 1 0.40625    3.6875    2.8125 6.026948 1.785922 123.9387 68.56287
    ##     drat_sd     wt_sd  qsec_sd     vs_sd     am_sd   gear_sd carb_sd
    ## 1 0.5346787 0.9784574 1.786943 0.5040161 0.4989909 0.7378041  1.6152

``` r
summarise_all(mtcars, ~mean(.) / sd(.))
```

    ##        mpg      cyl     disp      hp     drat       wt     qsec        vs
    ## 1 3.333466 3.464598 1.861581 2.13946 6.726586 3.288084 9.988426 0.8680278
    ##          am     gear    carb
    ## 1 0.8141431 4.997939 1.74127

``` r
# Rows where any value is missing
filter_all(nycflights13::weather, any_vars(is.na(.)))
```

    ## # A tibble: 3,109 x 15
    ##    origin  year month   day  hour  temp  dewp humid wind_dir wind_speed
    ##     <chr> <dbl> <dbl> <int> <int> <dbl> <dbl> <dbl>    <dbl>      <dbl>
    ##  1    EWR  2013     1     1    17 39.20 28.40 64.93      270   16.11092
    ##  2    EWR  2013     1     1    18 39.20 28.40 64.93      330   14.96014
    ##  3    EWR  2013     1     3    16 30.92 14.00 49.01       NA    4.60312
    ##  4    EWR  2013     1     6    10 33.80 30.20 86.49      210    4.60312
    ##  5    EWR  2013     1     6    12 33.80 32.00 93.03      220    9.20624
    ##  6    EWR  2013     1     6    13 35.60 32.00 86.59      240    8.05546
    ##  7    EWR  2013     1     6    14 35.60 32.00 86.59      230    8.05546
    ##  8    EWR  2013     1     6    15 37.40 30.20 74.98      250   11.50780
    ##  9    EWR  2013     1    11    17 44.96 35.96 70.52       NA    4.60312
    ## 10    EWR  2013     1    11    21 46.40 39.20 75.84       90    4.60312
    ## # ... with 3,099 more rows, and 5 more variables: wind_gust <dbl>,
    ## #   precip <dbl>, pressure <dbl>, visib <dbl>, time_hour <dttm>

``` r
# mutate_all would not work in the next line, we must add mutate_if
nycflights13::weather %>% mutate_if(., is.numeric, ~round(., digits = 2))
```

    ## # A tibble: 26,130 x 15
    ##    origin  year month   day  hour  temp  dewp humid wind_dir wind_speed
    ##     <chr> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>    <dbl>      <dbl>
    ##  1    EWR  2013     1     1     0 37.04 21.92 53.97      230      10.36
    ##  2    EWR  2013     1     1     1 37.04 21.92 53.97      230      13.81
    ##  3    EWR  2013     1     1     2 37.94 21.92 52.09      230      12.66
    ##  4    EWR  2013     1     1     3 37.94 23.00 54.51      230      13.81
    ##  5    EWR  2013     1     1     4 37.94 24.08 57.04      240      14.96
    ##  6    EWR  2013     1     1     6 39.02 26.06 59.37      270      10.36
    ##  7    EWR  2013     1     1     7 39.02 26.96 61.63      250       8.06
    ##  8    EWR  2013     1     1     8 39.02 28.04 64.43      240      11.51
    ##  9    EWR  2013     1     1     9 39.92 28.04 62.21      250      12.66
    ## 10    EWR  2013     1     1    10 39.02 28.04 64.43      260      12.66
    ## # ... with 26,120 more rows, and 5 more variables: wind_gust <dbl>,
    ## #   precip <dbl>, pressure <dbl>, visib <dbl>, time_hour <dttm>

`_at()` behaves like `select()` but needs helper options like vars and funs.

``` r
summarise_at(mtcars, vars(mpg, cyl), funs(min, max))
```

    ##   mpg_min cyl_min mpg_max cyl_max
    ## 1    10.4       4    33.9       8

`_if()` adds conditional logic to the aggregation function

``` r
summarise_if(nycflights13::weather,   # the data
             is.numeric,              # the conditional
             mean, na.rm = TRUE)      # the function
```

    ## # A tibble: 1 x 13
    ##    year    month      day     hour     temp    dewp    humid wind_dir
    ##   <dbl>    <dbl>    <dbl>    <dbl>    <dbl>   <dbl>    <dbl>    <dbl>
    ## 1  2013 6.505741 15.67972 11.51841 55.20351 41.3854 62.34732 198.0667
    ## # ... with 5 more variables: wind_speed <dbl>, wind_gust <dbl>,
    ## #   precip <dbl>, pressure <dbl>, visib <dbl>

``` r
# combo of _if and _all
summarise_if(
    nycflights13::weather,       # the data
    is.numeric,                  # the logical conditional
    mean, na.rm = TRUE           # the function
  ) %>%
  mutate_all(., ~round(., digits = 2))
```

    ## # A tibble: 1 x 13
    ##    year month   day  hour  temp  dewp humid wind_dir wind_speed wind_gust
    ##   <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>    <dbl>      <dbl>     <dbl>
    ## 1  2013  6.51 15.68 11.52  55.2 41.39 62.35   198.07       10.4     11.96
    ## # ... with 3 more variables: precip <dbl>, pressure <dbl>, visib <dbl>

``` r
mtcars %>% select_if(is.numeric) %>% map_dbl(., var)
```

    ##          mpg          cyl         disp           hp         drat 
    ## 3.632410e+01 3.189516e+00 1.536080e+04 4.700867e+03 2.858814e-01 
    ##           wt         qsec           vs           am         gear 
    ## 9.573790e-01 3.193166e+00 2.540323e-01 2.489919e-01 5.443548e-01 
    ##         carb 
    ## 2.608871e+00
