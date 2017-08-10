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
data.frame(matrix(nrow = 10, ncol = 4))
```

    ##    X1 X2 X3 X4
    ## 1  NA NA NA NA
    ## 2  NA NA NA NA
    ## 3  NA NA NA NA
    ## 4  NA NA NA NA
    ## 5  NA NA NA NA
    ## 6  NA NA NA NA
    ## 7  NA NA NA NA
    ## 8  NA NA NA NA
    ## 9  NA NA NA NA
    ## 10 NA NA NA NA

``` r
# empty data frame with assigned names
data.frame(matrix(nrow = 10, ncol = 4)) %>% setNames(., c("dv", "N1", "MN1", "SD1"))  
```

    ##    dv N1 MN1 SD1
    ## 1  NA NA  NA  NA
    ## 2  NA NA  NA  NA
    ## 3  NA NA  NA  NA
    ## 4  NA NA  NA  NA
    ## 5  NA NA  NA  NA
    ## 6  NA NA  NA  NA
    ## 7  NA NA  NA  NA
    ## 8  NA NA  NA  NA
    ## 9  NA NA  NA  NA
    ## 10 NA NA  NA  NA
