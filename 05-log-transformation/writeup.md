writeup
================
Logan King
10/12/2020

# Log transform

It is common in the analysis of biological data to log transform data
representing concentrations or data representing dose response.

## Part 1

The below examples will display how the probability and cumulative
density functions of distributions are affected by transformations and
will examine the differences between arithmetic and geometric means of
said distributions.

### Distribution 1

*X* ∼ GAMMA(shape = 3, scale = 1)

[Interactive plot (link)](https://www.desmos.com/calculator/wgqdkl5ogl)

<details>

<summary>See code here:</summary>

``` r
gamma_shape <- 3
gamma_scale <- 1
x <- seq(-5, 15, by = .1)

gamma_pdf <- dgamma(x, shape = gamma_shape, scale = gamma_scale)
gamma_cdf <- pgamma(x, shape = gamma_shape, scale = gamma_scale)

gamma_mean <- gamma_shape * gamma_scale
gamma_median <- qgamma(.5, shape = gamma_shape, scale = gamma_scale)
```

</details>

![](writeup_files/figure-gfm/gamma%20PDF-1.png)<!-- -->
![](writeup_files/figure-gfm/gamma%20CDF-1.png)<!-- -->

In the above PDF and CDF of the gamma distribution (given a shape of 3
and scale of 1), we see the density beginning to rise at 0 and reaching
peak density prior to reaching the median and mean values. The density
bottoms out at around 10. In the gamma distribution, we observe a
greater mean than median due to the skewed nature of the distribution.

<details>

<summary>See code here:</summary>

``` r
log_gamma <- log(rgamma(10000, shape = gamma_shape, scale = gamma_scale))

log_gamma_mean <- log(gamma_mean)
log_gamma_median <- log(gamma_median)
```

</details>

![](writeup_files/figure-gfm/transformed%20gamma%20PDF-1.png)<!-- -->

``` r
plot(ecdf(log_gamma), sub = 'mean = red; median = blue')
abline(v = log_gamma_mean, col = 'red')
abline(v = log_gamma_median, col = 'blue')
```

![](writeup_files/figure-gfm/transformed%20gamma%20CDF-1.png)<!-- -->

Using simulation, we are able to estimate a PDF (by way of the
histogram) and CDF (by way of ECDF) of the log transformation of the
gamma distribution. We now see a distribution which is much less skewed
and relatively close to normal. The density is represented over the
values of -3 to 3, as opposed to strictly non-negative values
previously. While the mean is still greater than the median, both values
appear closer to the maximum density than in the original gamma
distribution.

<details>

<summary>See code here:</summary>

``` r
arithmetic_mean <- NA
geometric_mean <- NA

for(i in 1:1000) {
    data = rgamma(100, shape = gamma_shape, scale = gamma_scale)
    arithmetic_mean[i] = mean(data)
    geometric_mean[i] = exp(mean(log(data)))
}
```

</details>

![](writeup_files/figure-gfm/means%20scatter%20gamma-1.png)<!-- -->

![](writeup_files/figure-gfm/means%20differences%20gamma-1.png)<!-- -->

When simulating arithmetic and geometric means for 1000 samples of size
100 for the gamma distribution, we observe each sample’s arithmetic mean
as greater than its geometric mean. This makes sense because the
arithmetic mean is drawn from the sample in absolute terms, while the
geometric mean is drawn from the sample in relative terms. There appears
to be a strong positive correlation between the two types of means,
based on the scatterplot.

### Distribution 2

*X* ∼ LOG NORMAL(*μ* =  − 1, *σ* = 1)

[Interactive plot (link)](https://www.desmos.com/calculator/rueernwrhl)

<details>

<summary>See code here:</summary>

``` r
lognormal_meanlog <- -1
lognormal_sdlog <- 1
x <- seq(-2, 10, by = .1)

lognormal_pdf <- dlnorm(x, meanlog = lognormal_meanlog, sdlog = lognormal_sdlog)
lognormal_cdf <- plnorm(x, meanlog = lognormal_meanlog, sdlog = lognormal_sdlog)

lognormal_mean <- exp(lognormal_meanlog + (lognormal_sdlog ^ 2) / 2)
lognormal_median <- qlnorm(.5, meanlog = lognormal_meanlog, sdlog = lognormal_sdlog)
```

</details>

![](writeup_files/figure-gfm/lognormal%20PDF-1.png)<!-- -->

![](writeup_files/figure-gfm/lognormal%20CDF-1.png)<!-- -->

In the above PDF and CDF of the lognormal distribution (given a log
scale mean of -1 and log scale sd of 1), we see the density beginning to
rise at 0 and reaching peak density prior to reaching the median and
mean values. The density bottoms out just after 6. In the lognormal
distribution, we observe a greater mean than median due to the skewed
nature of the distribution.

<details>

<summary>See code here:</summary>

``` r
log_lognormal <- log(rlnorm(10000, meanlog = lognormal_meanlog, sdlog = lognormal_sdlog))

log_lognormal_mean <- log(lognormal_mean)
log_lognormal_median <- log(lognormal_median)
```

</details>

![](writeup_files/figure-gfm/transformed%20lognormal%20PDF-1.png)<!-- -->

![](writeup_files/figure-gfm/transformed%20lognormal%20CDF-1.png)<!-- -->

Using simulation, we are able to estimate a PDF (by way of the
histogram) and CDF (by way of ECDF) of the log transformation of the
lognormal distribution. We now see a distribution which is much less
skewed and extremely close to normal. The density is represented over
the values of -4 to 2, as opposed to strictly non-negative values
previously. While the mean is still greater than the median, both values
appear closer to the maximum density than in the original lognormal
distribution.

<details>

<summary>See code here:</summary>

``` r
arithmetic_mean <- NA
geometric_mean <- NA

for(i in 1:1000) {
    data = rlnorm(100, meanlog = lognormal_meanlog, sdlog = lognormal_sdlog)
    arithmetic_mean[i] = mean(data)
    geometric_mean[i] = exp(mean(log(data)))
}
```

</details>

![](writeup_files/figure-gfm/means%20scatter%20lognormal-1.png)<!-- -->

![](writeup_files/figure-gfm/means%20differences%20lognormal-1.png)<!-- -->

When simulating arithmetic and geometric means for 1000 samples of size
100 for the lognormal distribution, we observe each sample’s arithmetic
mean as greater than its geometric mean. This makes sense because the
arithmetic mean is drawn from the sample in absolute terms, while the
geometric mean is drawn from the sample in relative terms. There appears
to be a weaker positive correlation between the two types of means for
this distribution than with the gamma distribution, based on the
scatterplot.

### Distribution 3

*X* ∼ UNIFORM(0, 12)

<details>

<summary>See code here:</summary>

``` r
unif_min <- 0
unif_max <- 12
x <- seq(-1, 13, by = .1)

unif_pdf <- dunif(x, min = unif_min, max = unif_max)
unif_cdf <- punif(x, min = unif_min, max = unif_max)

unif_mean_median <- (unif_min + unif_max) / 2
```

</details>

![](writeup_files/figure-gfm/uniform%20PDF-1.png)<!-- -->

![](writeup_files/figure-gfm/uniform%20CDF-1.png)<!-- -->

In the above PDF and CDF of the uniform distribution (given a minimum of
0 and maximum of 12), we see the density at a constant from 0 to 12 and
at 0 elsewhere. The mean and median are equivalent in this distribution.
The CDF increases at a constant rate over the 0 to 12 interval.

<details>

<summary>See code here:</summary>

``` r
log_unif <- log(runif(10000, min = unif_min, max = unif_max))

log_unif_mean_median <- log(unif_mean_median)
```

</details>

![](writeup_files/figure-gfm/transformed%20uniform%20PDF-1.png)<!-- -->

![](writeup_files/figure-gfm/transformed%20uniform%20CDF-1.png)<!-- -->

Using simulation, we are able to estimate a PDF (by way of the
histogram) and CDF (by way of ECDF) of the log transformation of the
uniform distribution. We now see a distribution which is much extremely
left skewed. The density is represented over the values of \~-10 to \~2,
as opposed to strictly values from 0 to 12 as previously seen. The mean
and median do not diverge from each other, despite the transformation.

<details>

<summary>See code here:</summary>

``` r
arithmetic_mean <- NA
geometric_mean <- NA

for(i in 1:1000) {
    data = runif(100, min = unif_min, max = unif_max)
    arithmetic_mean[i] = mean(data)
    geometric_mean[i] = exp(mean(log(data)))
}
```

</details>

![](writeup_files/figure-gfm/means%20scatter%20uniform-1.png)<!-- -->

![](writeup_files/figure-gfm/means%20differences%20uniform-1.png)<!-- -->

When simulating arithmetic and geometric means for 1000 samples of size
100 for the uniform distribution, we observe each sample’s arithmetic
mean as greater than its geometric mean. This makes sense because the
arithmetic mean is drawn from the sample in absolute terms, while the
geometric mean is drawn from the sample in relative terms. There appears
to be a strong positive correlation between the two types of means for
this distribution, based on the scatterplot.

## Part 2

Simulation can be used to prove that if *X*<sub>*i*</sub> \> 0 for all
*i*, then the arithmetic mean is greater than or equal to the geometric
mean.

<details>

<summary>See code here:</summary>

``` r
arithmetic_mean <- NA
geometric_mean <- NA

for(i in 1:10000) {
    data = runif(1000, min = 1, max = 2)
    arithmetic_mean[i] = mean(data)
    geometric_mean[i] = exp(mean(log(data)))
}
```

</details>

![](writeup_files/figure-gfm/means%20scatter%20uniform%20proof-1.png)<!-- -->

![](writeup_files/figure-gfm/means%20differences%20uniform%20proof-1.png)<!-- -->

Similarly to part 1, the arithmetic and geometric means can be found by
simulation. However, in order to prove that the arithmetic mean is
ALWAYS greater than the geometric mean, a much larger simulation and set
of samples are required. Here 10000 samples of size 1000 are drawn from
the uniform distribution (given a minimum of 1 and maximum of 2). We
observe every sample’s recorded arithmetic mean as greater than its
geometric mean. This makes sense because the arithmetic mean is drawn
from the sample in absolute terms, while the geometric mean is drawn
from the sample in relative terms. There appears to be a strong positive
correlation between the two types of means for this distribution, based
on the scatterplot.

## Part 3

In the relationship between *E*\[log (*X*)\] and log (*E*\[*X*\]),
log (*E*\[*X*\]) is always larger.

<details>

<summary>See code here:</summary>

``` r
expectation_log <- NA
log_expectation <- NA

for(i in 1:10000) {
    data = runif(1000, min = 1, max = 2)
    expectation_log[i] = mean(log(data))
    log_expectation[i] = log(mean(data))
}
```

</details>

![](writeup_files/figure-gfm/expextation%20uniform%20scatter-1.png)<!-- -->

![](writeup_files/figure-gfm/expectation%20differences%20hist-1.png)<!-- -->

Similarly to how the interactions of arithmetic and geometric means can
be found by simulation, the interaction between transformed expectations
can be found by simulation. Here 10000 samples of size 1000 are drawn
from the uniform distribution (given a minimum of 1 and maximum of 2).
The mean of each log transformation sample is compared with the log
transformation of each sample mean. We observe log (*E*\[*X*\]) to
always be greater. This makes sense, because the log of an absolute
expectation will be greater than the expectation of a relative measure.
Furthermore, there appears to be a strong positive correlation between
the two transformations, based on the scatterplot.
