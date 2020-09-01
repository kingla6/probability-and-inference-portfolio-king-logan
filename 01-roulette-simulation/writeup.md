writeup
================
Logan King
8/31/2020

# Using Computer Simulation to Analyze the Martingale Strategy in Roulette

Despite being notorious for having the worst odds to win of the various
casino game offerings, people gather around the roulette table day after
day with hopes of winning big. Several strategies have been developed to
try and improve roulette outcomes, one of which being the [Martingale
strategy](https://github.com/thomasgstewart/data-science-5620-Fall-2020/blob/master/deliverables/01-roulette.md).

With simulation, it is possible to calculate the average earnings for a
player utilizing the Martingale strategy over many repetitions. This
will allow for evaluation of whether or not Martingale is a profitable
strategy. To begin, several functions must be created. A function for a
single play of the roulette game, a function for when play should cease,
and a function for an overall series of plays.

## Single Play Function

The single play function takes several inputs which describe the game
state prior to a spin of the roulette wheel. The output for the function
is the game state following a spin of the wheel. The parameters which
make up the game state are: player budget, maximum budget threshold for
stopping play (essentially one’s ideal end cash amount to be satisfied
with no longer playing), the maximum allowed number of plays, the
casino’s wager limit, the number of plays that have occurred, the
previous wager amount, and the previous outcome (win/loss). These
parameters are used for the input of the single play function and will
be updated as the output, following one play.

## Stopping Function

The stopping function is necessary for construction of the function for
a series of plays. The stopping function is an indicator of whether or
not play at the roulette table should continue. There are three
instances in which this function will trigger the game to end: when the
budget reaches zero, when the maximum number of plays have been reached,
and when the budget is greater than the max budget threshold. If none of
the previously mentioned game state conditions have been met, play will
continue.

## Series of Play Function

The series of play function executes the one play function on a given
set of parameters repeatedly until the stopping function triggers the
series to end. Given initial inputs of budget, winning threshold,
maximum number of plays, and maximum wager amount, the series function
can calculate the final budget, earnings, and number of plays over a
series of roulette spins.

## Simulation

Using simulation, we can specify the amount of series of roulette plays
to be simulated. With simulation, the long run viability of the
Martingale strategy can be evaluated. For the purposes of this exercise,
10,000 series will be simulated. The end budget result of each series is
then stored in a vector to allow for calculation of average earnings.

## Average Earnings

When simulated 10,000 times given the following parameters:

  - Starting Budget: $200
  - Winning Threshold: $300
  - Max Plays: 1000
  - Max Wager: $100

Over 10,000 repetitions, we see that the Martingale strategy is not
profitable, as the average earnings are $-45.6885.

## Earnings Over a Series

![](writeup_files/figure-gfm/earnings%20over%20a%20series-1.png)<!-- -->![](writeup_files/figure-gfm/earnings%20over%20a%20series-2.png)<!-- -->![](writeup_files/figure-gfm/earnings%20over%20a%20series-3.png)<!-- -->

The above plots each display examples of a single series of roulette
plays. In the first two, the Martingale strategy provides earnings of
$100 (a final budget of $300) after approximately 200 and 250 turns,
respectively. In the third series example, the strategy leaves the
gambler with -$200 in earnings (a final budget of $0) after just 8
turns.

## Changing Parameters

A change in the parameters allows us to see the interaction with each on
the average earnings. As each individual parameter changes, the other
parameters hold their default value seen in the initial simulation.

![](writeup_files/figure-gfm/parameter%20change%20plots-1.png)<!-- -->![](writeup_files/figure-gfm/parameter%20change%20plots-2.png)<!-- -->![](writeup_files/figure-gfm/parameter%20change%20plots-3.png)<!-- -->![](writeup_files/figure-gfm/parameter%20change%20plots-4.png)<!-- -->

As seen in the above plots, altering the parameters doesn’t ever make
the Martingale strategy profitable in the long run. However, changing
certain parameters may serve to mitigate long-term losses. Increasing
the initial starting budget causes earnings to approach zero in the long
term. Meanwhile, a winning threshold close to the initial starting
budget mitigates long term losses. For casino-implemented rules of
maximum plays and maximum wager amount, behavior is mirrored. Generally,
losses are mitigated by fewer plays and a higher max wager.

## Average Number of Plays

Similar to how the average earnings over 10,000 simulated series were
calculated, the average number of plays is able to be calculated.
Instead of recording the earnings of each series in a vector and taking
the mean, the number of turns in a series is recorded. The average
number of turns over 10,000 repetitions is 202.522.

## Limitations

As with any model, the simulation method of the Martingale strategy has
limitations. The simulation does not take into account any payout rules
that the casino may have enforced for the roulette game, we assume a 1:1
payout. Additionally, the model assumes that the player is placing bets
on red each turn. It is important to note that while a roulette player
may see success over a given series or group of series, simulation looks
at the long term outcome expectation.
