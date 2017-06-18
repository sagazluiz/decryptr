
decryptr
========

### Description

`decryptr` is an R package to break CAPTCHAs. It is also an extensible tool built in a way that enables anyone to contribute with their own CAPTCHA-breaking code.

To install `decryptr`, simply run the code below:

``` r
if (!require(devtools)) install.packages("devtools")
devtools::install_github("decryptr/decryptr")
```

It is also recommended to install `decryptrModels` package.

``` r
devtools::install_github("decryptr/decryptrModels")
```

    ## Using GitHub PAT from envvar GITHUB_PAT

    ## Skipping install of 'decryptrModels' from a github remote, the SHA1 (e8429064) has not changed since last install.
    ##   Use `force = TRUE` to force installation

### Basic usage

`decryptr` has functions for downloading and breaking captchas from multiple sources, one of which is TJMG (a court in southern Brazil). Here is how one could break captchas from TJMG:

#### Download and visualise

``` r
library(decryptr)

# Download captchas
captcha <- download_tjmg(dest = 'img')

# Draw captcha
captcha %>% 
  read_captcha() %>% 
  plot()
```

![](README_files/figure-markdown_github-ascii_identifiers/captcha-1.png)

#### Classify manually

``` r
captcha %>% 
  read_captcha() %>% 
  classify()
# plots the image and opens the console
#> Answer: 
```

#### Load model and predict

``` r
# Load model (install decryptrModels package first)
keras_tjmg <- decryptrModels::read_model('tjmg')
data('tjmg', package = 'decryptr')
tjmg$model <- keras_tjmg

captcha %>% 
  read_captcha() %>% 
  predict(tjmg, arq = .)
```

    ## [1] "84232"

### Performance

Once loaded to memory, Keras models are extremely fast. Also, we don't run any pre-processing on the image.

``` r
break_captcha <- function() {
  captcha %>% 
    read_captcha() %>% 
    predict(tjmg, arq = .)
}

microbenchmark::microbenchmark(break_captcha())
```

    ## Unit: milliseconds
    ##             expr      min       lq     mean  median       uq      max
    ##  break_captcha() 12.54039 13.39467 16.42615 14.0669 16.00585 172.3118
    ##  neval
    ##    100

### Extensibility

Since `decryptr` is built on top of S3 methods, anyone can extend its functionalities with custom packages. Simply create a `download_<ext>` function, a `read_<ext>` function, and a `predict.<ext>` function and you're good to go.

More information about extensibility can be found on `decryptr`'s documentation.
