
<!-- README.md is generated from README.Rmd. Please edit that file -->

# blazr <a href="https://r-staceans.github.io/blazr/"><img src="man/figures/logo.png" align="right" height="138" /></a>

<!-- badges: start -->

[![R-CMD-check](https://github.com/r-staceans/blazr/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/r-staceans/blazr/actions/workflows/R-CMD-check.yaml)
[![Codecov test
coverage](https://codecov.io/gh/r-staceans/blazr/graph/badge.svg)](https://app.codecov.io/gh/r-staceans/blazr)
[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://lifecycle.r-lib.org/articles/stages.html#experimental)
<!-- badges: end -->

⚠️ **This package is a work in progress and is not yet ready for use.**

The goal of blazr is to provide R with a blazingly fast interface to
multi-threading in Rust.

## Installation

Install the development version of `blazr` from GitHub with:

``` r
# install.packages("pak")
pak::pak("r-staceans/blazr")
```

## Example

Here’s a simple example, computing the sum of a vector of integers,
using multiple threads:

``` r
library(blazr)

create_int_vector <- function(n) {
  set.seed(42)
  sample.int(100, n, replace = TRUE)
}

n <- 1e8

x <- create_int_vector(n)

blazr::sum_with_threads(
  x,
  n = 2L
)
#> [1] 754832969
```

## Benchmarking

When running this sum against very large numbers, we can see the
performance benefits of using multiple threads:

``` r
library(bench)
library(ggplot2)

results <- bench::press(
  size = c(1e7, 1e8, 1e9),
  {
    x = create_int_vector(n)
    bench::mark(
      single_thread = {
        blazr::sum_with_threads(
          x,
          n = 1L
        )
      },
      multi_thread = {
        blazr::sum_with_threads(
          x,
          n = 4L
        )
      }
    )
  })
#> Running with:
#>         size
#> 1   10000000
#> 2  100000000
#> 3 1000000000

ggplot2::autoplot(results)
#> Loading required namespace: tidyr
```

<img src="man/figures/README-benchmark-1.png" width="100%" />
