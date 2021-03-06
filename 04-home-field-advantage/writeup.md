writeup
================
Logan King
9/27/2020

# If home field advantage exists, how much of an impact does it have on winning the world series?

Home field advantage is the edge which teams are perceived to gain when
playing games at their home stadium. In this blog, we will explore the
effects of home field advantage in a World Series matchup between the
Atlanta Braves and New York Yankees through analytical and simulation
methods.

<details>

<summary>See code here:</summary>

``` r
library(tidyverse)
```

    ## -- Attaching packages ---------------------------------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.2     v purrr   0.3.4
    ## v tibble  3.0.3     v dplyr   1.0.2
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.5.0

    ## -- Conflicts ------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(data.table)
```

    ## 
    ## Attaching package: 'data.table'

    ## The following objects are masked from 'package:dplyr':
    ## 
    ##     between, first, last

    ## The following object is masked from 'package:purrr':
    ## 
    ##     transpose

``` r
library(ggplot2)
```

``` r
# Get all possible outcomes
apo <- fread("C:/Users/kingl/Desktop/Projects/probability_fall20/probability-and-inference-portfolio-king-logan/assets/all-possible-world-series-outcomes.csv")
```

</details>

## Analytical Probability

Analytical probability is found by taking the entire range of possible
outcomes and analyzing the probability of each outcome. For example if a
fair coin is flipped three times, the possible range of outcomes is
{HHH, HHT, HTH, TTH, THH, ,THT, HTT, TTT}. Each outcome has a
probability of 1/8 or (.5 \* .5 \* .5) and the analytical probability of
at least two heads can be calculated from this set by summing the
probabilities of all outcomes with at least two heads.

Much like the coin example, the analytical probability of the outcome of
the World Series can be calculated. In order to calculate this
probability, we first need the set of all possible outcomes of the World
Series games. That is provided in a previously loaded dataset. As it
turns out, there are 35 possible outcomes for each team to win the World
Series. However, the likelihood of a team emerging victorious depends on
several factors.

Unlike the fair coin example, the probability of victory for the Braves
and Yankees are not even. For this exercise, the Braves will have a 55%
chance of winning (45% chance of losing) a single game in the series in
the absence of home field advantage. In order to observe the effects of
home field advantage, a 10% probability of victory bonus will be given
to the home team in a matchup. Effectively, this gives the braves a
60.5% chance of victory (39.5% chance of defeat) in a single home
matchup and a 50.5% chance of victory (49.5% chance of defeat) in a
single road matchup.

For a series in which the sequence of game locations is {NYC, NYC, ATL,
ATL, ATL, NYC, NYC}, each combination of single game location (Home or
Away) and result (Win or Loss) is assigned its associated probability.
Each probability in the series is multiplied together in order to find
the probability of that specific series outcome across the entire set of
possible World Series outcomes. By summing up the probability of each
series in which the outcome is a win, we can find the analytical
probability of the Braves winning the world Series under the following
parameters:

  - Game Locations: {NYC, NYC, ATL, ATL, ATL, NYC, NYC}
  - Probability that the Braves win a single game: 0.55
  - Home Field Advantage Multiplier (1.0, 1.1)
      - we can find the effects of both no observed home field advantage
        and observed home field advantage

<details>

<summary>See code here:</summary>

``` r
analytical_ws <- function(hfi, pb, adv_mult) {
  pbh <- pb * adv_mult
  pba <- 1 - (1 - pb) * adv_mult
  # Calculate the probability of each possible outcome
  apo[, p := NA_real_] # Initialize new column in apo to store prob
  for(i in 1:nrow(apo)){
    prob_game <- rep(1, 7)
    for(j in 1:7){
      p_win <- ifelse(hfi[j], pbh, pba)
      prob_game[j] <- case_when(
          apo[i,j,with=FALSE] == "W" ~ p_win
        , apo[i,j,with=FALSE] == "L" ~ 1 - p_win
        , TRUE ~ 1
      )
    }
    apo[i, p := prod(prob_game)] # Data.table syntax
  }
  if(apo[, sum(p)] %>% near(1)) {
    return(apo[, sum(p), overall_outcome])
  }
}

analytical_adv <- analytical_ws(hfi = c(0,0,1,1,1,0,0), pb = .55, adv_mult = 1.1)$V1[1]
analytical_no_adv <- analytical_ws(hfi = c(0,0,1,1,1,0,0), pb = .55, adv_mult = 1)$V1[1]
```

</details>

We find that the analytical probability of the Braves winning the World
Series when home field advantage plays a factor is 60.422% while the
analytical probability of the Braves winning the World Series when home
field advantage does not exist is 60.829%. The presence of a home field
advantage actually harms the Braves, because they have less home games
than the Yankees in the event that the series lasts all seven games.
However, they are more likely than not to win the World Series in both
instances, because their single game winning probability never dips
below 50% in either scenario.

## Simulated Probability

Much like analytical probability was calculated, we can use simulation
to approximate the probability of a team winning the World Series.
Simulation is different in analytical probability in that it will
provide an approximation of the true probability over a given set of
iterations as opposed to the true probability value like the analytical
method does. The simulation method can give a good representation of the
variation of the outcomes of probabilistic events which can be seen in
the short term, however in the long term it approaches the true
probability given by the analytical method.

Simulation is calculated by first determining the outcome of a single
series. Given the inputs of location, win probability, and home field
advantage multiplier, an output of win or loss is generated for a single
game. This single game operation is repeated until the series is
completed by either winning or losing four games. In order to simulate
the probability of the Braves winning the World Series, each individual
series can be replicated 100,000 times to give a reliable approximation
of the true probability.

<details>

<summary>See code here:</summary>

``` r
set.seed(100)

simulated_ws <- function(hfi, pb, adv_mult) {
  pbh <- pb * adv_mult
  pba <- 1 - (1 - pb) * adv_mult
  win_count <- 0
  for(i in 1:7) {
    if(hfi[i] == 1){
      p_win <- pbh
    } else {
      p_win <- pba
    }
    game_outcome <- rbinom(1, 1, p_win)
    win_count <- win_count + game_outcome
    if(win_count == 4 | (i - win_count) == 4) break
  }
  return(win_count == 4)
}
```

``` r
sim_outcome_adv <- NA
for(i in 1:100000){
  sim_outcome_adv[i] <- simulated_ws(hfi = c(0,0,1,1,1,0,0), pb = .55, adv_mult = 1.1)
}
simulation_adv <- mean(sim_outcome_adv)

sim_outcome_no <- NA
for(i in 1:100000){
  sim_outcome_no[i] <- simulated_ws(hfi = c(0,0,1,1,1,0,0), pb = .55, adv_mult = 1)
}
simulation_no_adv <- mean(sim_outcome_no)
```

</details>

After running the simulation, we see that the simulated probability for
the Braves to win the World Series (with the same parameters as
previously established) is 60.37% with the effect of home field
advantage and 60.844% without a home field advantage effect.

As was discussed previously, simulation will always display some amount
of variation from the true probability - which was seen above in our
simulation results. This variation can be explained in terms of absolute
and relative error. Absolute error is the total amount that an observed
probability varies from the true probability, while relative error is
the absolute error as a proportion of the true probability (essentially
the error in the context of the true probability).

<details>

<summary>See code here:</summary>

``` r
ae_adv <- abs(simulation_adv-analytical_adv)
ae_no_adv <- abs(simulation_no_adv-analytical_no_adv)

re_adv <- ae_adv/analytical_adv
re_no_adv <- ae_no_adv/analytical_no_adv
```

</details>

The absolute error for the simulation with home field advantage is
5.210^{-4} while the absolute error without home field advantage is
1.510^{-4}. The relative error seen in the simulations for home field
advantage and no home field advantage is 8.610^{-4} and 2.510^{-4},
respectively.

## Bonus

We can also explore the effect of home field advantage in relation to
single game winning probability and their effect on the probability of
winning a series. The below plot uses analytical probability
calculations to capture these interactions by displaying varying lines
for both home field advantage and no home field advantage along with a
varying single game probability axis in order to see how the probability
of winning a World Series (located on the other axis) is influenced. Two
plots are displayed, one for a series with 4 home games and one for a
series with 4 away games.

<details>

<summary>See code here (plot code hidden by echo=FALSE in next
chunk):</summary>

``` r
pb_comparison <- expand.grid(pb = seq(0, 1, by = .02),
                             adv_mult = c(1.0,1.1),
                             ws_win_prob_h = NA_real_,
                             ws_win_prob_a = NA_real_,
                             KEEP.OUT.ATTRS = F)

for(i in 1:nrow(pb_comparison)){
  pb_comparison[i,'ws_win_prob_a'] <- analytical_ws(hfi = c(0,0,1,1,1,0,0), 
                                                    pb = pb_comparison[i,'pb'], 
                                                    adv_mult = pb_comparison[i,'adv_mult'])$V1[1]
  pb_comparison[i,'ws_win_prob_h'] <- analytical_ws(hfi = c(1,1,0,0,0,1,1),
                                                    pb = pb_comparison[i,'pb'],
                                                    adv_mult = pb_comparison[i,'adv_mult'])$V1[1]
}
```

</details>

![](writeup_files/figure-gfm/plot%20for%20bonus%20questions-1.png)<!-- -->

From the above plots, several conclusions can be made. When a series is
primarily away games for a team, no advantage is better than a 10% home
field advantage boost when the probability of winning a single game is
below approximately 55%. Above that point, home field advantage becomes
more beneficial. This trend is reversed when a series is primarily home
games for a team. In this instance, no home field advantage is only
ideal up until approximately 45% probability of winning a single game.
After that point, home field advantage is more beneficial.

## Problem 5 Update

<details>

<summary>See code here (plot code hidden by echo=FALSE in next
chunk):</summary>

``` r
p5_update <- expand.grid(pb = .55,
                         adv_mult = seq(1, 2, by = .01),
                         four_home_games = NA_real_,
                         four_away_games = NA_real_,
                         KEEP.OUT.ATTRS = F)

for(i in 1:nrow(p5_update)){
  p5_update[i,'four_away_games'] <- analytical_ws(hfi = c(0,0,1,1,1,0,0), 
                                                pb = p5_update[i,'pb'], 
                                                adv_mult = p5_update[i,'adv_mult'])$V1[1]
  p5_update[i,'four_home_games'] <- analytical_ws(hfi = c(1,1,0,0,0,1,1),
                                                pb = p5_update[i,'pb'],
                                                adv_mult = p5_update[i,'adv_mult'])$V1[1]
}
```

</details>

    ## Warning: Removed 19 row(s) containing missing values (geom_path).

![](writeup_files/figure-gfm/updated%20plot-1.png)<!-- -->

We see that the difference in probability of winning the world series
does depend on the home field advantage factor. A team with a 55%
probability of winning a single game sees around a 60% chance of winning
the WS when there is no home field advantage factor. As the effect of
home field advantage increases, the probability of winning the WS
increases if the team plays a majority of home games and decreases as
the team plays a majority of away games.
