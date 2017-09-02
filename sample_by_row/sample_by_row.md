sample and average by row
================
JP
August 31, 2017

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
df <- tibble(
    X1 = rnorm(n = 5, mean = 0, sd = 1),
    X2 = rnorm(n = 5, mean = 0, sd = 1),
    X3 = rnorm(n = 5, mean = 0, sd = 1)
)
```

Use list columns to concatenate row values and then apply `sample()` and `mean()` functions

``` r
df <- df %>% 
  rowwise() %>% 
  mutate(
    X4 = list(c(X1, X2, X3)),
    X4_rand = list(sample(X4, 2, replace = FALSE)),
    X4_rand_mean = mean(X4_rand)
  )
```

``` r
df$X4[[1]] 
```

    ## [1] 1.4962975 0.5924513 0.7250807

``` r
df$X4_rand[[1]] 
```

    ## [1] 1.4962975 0.5924513

``` r
df$X4_rand_mean[[1]] 
```

    ## [1] 1.044374

``` r
df$X4[[2]] 
```

    ## [1] -1.97946379  1.31622442  0.07540881

``` r
df$X4_rand[[2]] 
```

    ## [1] -1.979464  1.316224

``` r
df$X4_rand_mean[[2]] 
```

    ## [1] -0.3316197
