writeup
================
Logan King
9/21/2020

<details>

<summary>See code here:</summary>

``` r
library(tidyverse)
```

    ## -- Attaching packages ----------------------------------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.2     v purrr   0.3.4
    ## v tibble  3.0.3     v dplyr   1.0.2
    ## v tidyr   1.1.1     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.5.0

    ## -- Conflicts -------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(knitr)
```

</details>

Discrete probability functions can be used for a variety of
applications. The following article will use discrete probability
functions to solve problems related to the Major League Baseball World
Series. The World Series is a best-of-seven matchup between the MLB’s
National and American League champions which decides the champion of the
MLB each season.

## Setup:

1.  Suppose that the Braves and the Yankees are teams competing in the
    World Series.
2.  Suppose that in any given game, the probability that the Braves win
    is P<sub>B</sub> and the probability that the Yankees win is
    P<sub>Y</sub>=1−P<sub>B</sub>.

## Questions to answer:

### 1\. What is the probability that the Braves win the World Series given that P<sub>B</sub> = 0.55?

The probability of the Braves to win a single game in the series is
given as .55 and their probability to win the World Series can be
calculated using a negative binomial distribution. The negative binomial
distribution is a discrete probability distribution which allows us to
model the number of successes of an event before a specified number of
errors occurs. In this case, we are interested in the Braves reaching
four wins in a maximum of seven games. In negative binomial terms, what
is the probability of reaching four successes while only reaching a
maximum of three (0, 1, 2, or 3) failures if the given probability of
success is .55? It turns out the probability for the Braves to win the
World Series with these parameters is 0.608.

### 2\. What is the probability that the Braves win the World Series given that P<sub>B</sub> = x? This will be a figure (see below) with P<sub>B</sub> on the x-axis and P(Braves win World Series) on the y-axis.

<details>

<summary>See code here (plot code hidden by echo=FALSE in next
chunk):</summary>

``` r
pb <- seq(0, 1, by = .001)
pws <- pnbinom(3, 4, pb)
```

    ## Warning in pnbinom(3, 4, pb): NaNs produced

</details>

![](writeup_files/figure-gfm/question%202%20plot-1.png)<!-- -->

It is clear that as the probability of the Braves winning a single game
increases from 0 to 1, the probability that they win the entire World
Series increases from 0 to 1.

### 3\. Suppose one could change the World Series to be best-of-9 or some other best-of-X series. What is the shortest series length so that P(Braves win World Series | P<sub>B</sub> = .55) ≥ 0.8?

<details>

<summary>See code here (plot code hidden by echo=FALSE in next
chunk):</summary>

``` r
win <- seq(4, 100, by = 1)
lose <- win - 1
games <- win + lose
pws <- pnbinom(lose,win,.55)
```

</details>

![](writeup_files/figure-gfm/question%203%20plot-1.png)<!-- -->

In the event that the length of the series is able to be altered to a
best-of-x series, the shortest series required to give the Braves at
least an 80% chance to win the World Series is 71 games-if the chance
they win a single game is held constant at 55%.

### 4\. What is the shortest series length so that P(Braves win World Series | P<sub>B</sub> = x) ≥ 0.8? This will be a figure (see below) with PB on the x-axis and series length is the y-axis.

<details>

<summary>See code here (plot code hidden by echo=FALSE in next
chunk):</summary>

``` r
pb <- seq(0, 1, by = .001)
least_games <- NA

for(i in 1:length(pb)) {
  win <- seq(1, 1000, by = 1)
  lose <- win - 1
  games <- win + lose
  pws <- pnbinom(lose, win, pb[i])
  least_games[i] <- games[which(pws>=.8)][1]
}
```

    ## Warning in pnbinom(lose, win, pb[i]): NaNs produced

</details>

![](writeup_files/figure-gfm/question%204%20plot-1.png)<!-- -->

In order for the Braves to have at least 80% chance to win the series as
both the length of the series and the probability that the Braves win a
single game changes, the probability that the Braves win a single game
must be greater than 50%. At 50%, the length of the series required for
an 80% chance to win approaches infinity. However, as the probability to
win a single game increases, the length of series required drastically
decreases, approaching 1 as P<sub>B</sub> approaches 1.

### 5\. Calculate P(P<sub>B</sub> = 0.55 | Braves win World Series in 7 games) under the assumption that either P<sub>B</sub> = 0.55 or P<sub>B</sub> = 0.45. Explain your solution.

We begin with the assumption that P<sub>B</sub> can be either .55 or
.45. Without any prior knowledge about the team it is equally likely for
P<sub>B</sub> to take either of the aforementioned values. We conclude
that the marginal probability of (P<sub>B</sub> = .55) = (P<sub>B</sub>
= .45) = .5.

Next, we compute P(Braves Win World Series in 7 Games | P<sub>B</sub> =
.55) and P(Braves Win World Series in 7 Games | P<sub>B</sub> = .45). In
other words, the probability that the Braves win the World Series in
exactly seven games given a specific probability to win a single game.
This is done using the negative binomial density function, which tells
us the probability of four successes (wins) and exactly three failures
(losses) given a certain probability of success (.55 or .45). We find
P(Braves Win World Series in 7 Games | P<sub>B</sub> = .55) and P(Braves
Win World Series in 7 Games | P<sub>B</sub> = .45) to be 0.167 and
0.136, respectively. Additionally, we are able to compute the
probability that the Braves do not win the world series in exactly seven
games given each single game probability (P(Braves Do Not Win World
Series in 7 Games | P<sub>B</sub> = .55) and P(Braves Do Not Win World
Series in 7 Games | P<sub>B</sub> = .45)). This is done simply by
subtracting the probabilities that they do win the World Series in
exactly seven games from one, giving us 0.833 at the P<sub>B</sub> = .55
and 0.864 at P<sub>B</sub> = .45.

Next, we incorporate our prior knowledge-or lack thereof-into our
recently evaluated conditional probabilities. Multiplying each recently
evaluated conditional probability by the marginal probability of
P<sub>B</sub> = .55 and P<sub>B</sub> = .45, which happens to be .5,
uncovers several cell probabilities. Cell probabilities take the form of
P(P<sub>B</sub> = 0.55 & Braves Win World Series in 7 games),
P(P<sub>B</sub> = 0.45 & Braves Win World Series in 7 games),
P(P<sub>B</sub> = 0.55 & Braves Do Not Win World Series in 7 games), and
P(P<sub>B</sub> = 0.45 & Braves Do Not Win World Series in 7 games). In
other words, the probability of each event happening at the same time.
In the order listed before, those cell probabilities are 0.083, 0.068,
0.417, 0.432.

With knowledge of each cell probability, the probability of the Braves
winning the World Series in exactly seven games and not winning the
World Series in exactly seven games can be calculated. This is done
simply by summing the previously calculated cell probabilities in each
column. We find that P(Braves Win World Series in 7 Games) = 0.152 and
P(Braves Do Not Win World Series in 7 Games) = 0.848.

Finally, we are able to address the question at hand: P(P<sub>B</sub> =
.55 | Braves Win World Series in 7 Games). This is calculated by
dividing the cell probability of P(P<sub>B</sub> = .55 & Braves Win
World Series in 7 Games) by the column probability of P(Braves Win World
Series in 7 Games). The calculation leaves us with 0.55. We have found
the likelihood of the the Braves’ winning a single game being .55 given
that they win the World Series in exactly 7 games is, in fact 0.55.

| row\_names                  | win\_in\_7 | not\_win\_in\_7 | marginal\_row\_probability |
| :-------------------------- | :--------- | :-------------- | :------------------------- |
| PB=.55 Cell Probability     | 0.083      | 0.417           | 0.5                        |
| PB=.55 Row Probability      | 0.167      | 0.833           | NA                         |
| PB=.55 Column Probability   | 0.55       | 0.491           | NA                         |
| PB=.45 Cell Probability     | 0.068      | 0.432           | 0.5                        |
| PB=.45 Row Probability      | 0.136      | 0.864           | NA                         |
| PB=.45 Column Probability   | 0.45       | 0.509           | NA                         |
| Marginal Column Probability | 0.152      | 0.848           | 1                          |

\*A look at the probability table that was constructed
