Map functions
================
JP
July 6, 2017

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

map same formula across several subsets of your databy using `split()` with `map()`
===================================================================================

This will create a list of models.

``` r
mtcars %>%
  split(.$cyl) %>% 
  map(~lm(mpg ~ wt * am, data = .))
```

    ## $`4`
    ## 
    ## Call:
    ## lm(formula = mpg ~ wt * am, data = .)
    ## 
    ## Coefficients:
    ## (Intercept)           wt           am        wt:am  
    ##      13.896        3.068       30.299      -10.961  
    ## 
    ## 
    ## $`6`
    ## 
    ## Call:
    ## lm(formula = mpg ~ wt * am, data = .)
    ## 
    ## Coefficients:
    ## (Intercept)           wt           am        wt:am  
    ##       63.65       -13.14       -41.45        12.55  
    ## 
    ## 
    ## $`8`
    ## 
    ## Call:
    ## lm(formula = mpg ~ wt * am, data = .)
    ## 
    ## Coefficients:
    ## (Intercept)           wt           am        wt:am  
    ##     25.0594      -2.4389      -2.9194       0.4389
