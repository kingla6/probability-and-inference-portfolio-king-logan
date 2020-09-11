writeup
================
Logan King
9/7/2020

Probability is a concept that is prevalent in everyday life. Answers to
questions such as “How likely is this coin to land on heads?” to “How
likely is it that the wait for my morning coffee will be longer than 5
minutes?” can be estimated with probability theory. Using computer
simulation, a probabilistic event can be replicated many times to see
how much the observed outcome differs from the expectation. This
diversion of the observed outcome from the expected outcome is known as
error. Error can be viewed through the lens of absolute and relative
error, defined mathematically below:

  - Absolute Error = | phat - p |
  - Relative Error = | phat - p | / p

Absolute error is the absolute value of the observed outcome (phat)
minus the expected outcome (p). Essentially, absolute error is the total
difference between what is seen in real life and what is expected to
happen. Relative error is absolute error, divided by the expected
outcome. The benefit to relative error is that it allows for
contextualization of the error value in terms of the expected
probability. The concept of error is important, because it can make or
break the reliability of the results of a simulation.

To display the concepts of absolute and relative error, 10,000
simulations of a binomial event (an event with two outcomes: success or
failure) with varying probabilities for success are run for a given
number of sample sizes. For a each set of simulations, the mean absolute
and relative errors are recorded and plotted.

<details>

<summary>See code here:</summary>

``` r
library(magrittr)
library(tgsify)
```

    ## Loading required package: data.table

    ## Loading required package: dplyr

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:data.table':
    ## 
    ##     between, first, last

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

    ## Loading required package: dtplyr

    ## Loading required package: showtext

    ## Loading required package: sysfonts

    ## Loading required package: showtextdb

``` r
parameters <- list(rep = NA, ss = NA, p = NA)

create_data_estimate_p <- function(parameters){
  parameters$phat <- rbinom(parameters$rep, parameters$ss, parameters$p)/parameters$ss
  parameters
}

absolute_error <- function(parameters){
  abs(parameters$phat-parameters$p)
}

one_p_n <- function(parameters){
  ae <- parameters %>% create_data_estimate_p %>% absolute_error
  re <- ae/parameters$p
  mae <- mean(ae)
  mre <- mean(re)
  c(mae,mre)
}
```

``` r
simulation_settings <- expand.grid(
  rep = 10000,
  p = c(.01,.05,.1,.25,.5),
  ss = 2^(2:15),
  mae = NA_real_,
  mre = NA_real_,
  KEEP.OUT.ATTRS = F
)

for (i in 1:nrow(simulation_settings)){
  simulation_settings[i,c('mae','mre')] <- simulation_settings[i,] %>% as.list() %>% one_p_n()
}
```

</details>

![](writeup_files/figure-gfm/plot%20for%20MAE-1.png)<!-- -->

From this plot, it is clear that the mean absolute error is highest for
all probabilities at the smallest sample size. No matter the probability
of a given event, it should be conducted many times in order to observe
the least amount of absolute error. A good rule of thumb would be to
conduct an event at least 10,000 times, as the error begins to approach
zero at this sample size. Another clear takeaway from this plot is the
higher error levels for higher probabilities at low sample sizes,
reinforcing the importance of a high sample size of events.

![](writeup_files/figure-gfm/plot%20for%20MRE-1.png)<!-- -->

Once again, the importance of a large sample size for events is seen as
the mean relative error approaches zero for all probability levels after
10,000. However, the error levels at low sample sizes are flipped for
the probabilities as compared to the previous plot. Relative error
levels for low probabilities are higher than those of the high
probabilities at the low sample sizes. This is likely due to low
probabilities being penalized more when an error is experienced, as the
expected probability value is the denominator in the relative error
equation.

Whether simulating a coin flip or the length of your wait for coffee, be
sure to include a high number of samples in your simulation for the
greatest accuracy. A low number of samples could cause error in your
simulated wait for coffee and make you late for work\!
