# HalfLife_Models
This project aims to develop Stan models to calculate half-lives in various experimental setups.

The models are included in the Simulations.Rmd file. All the models are 2-compartment models, with an entry compartment where appropriate (e.g. IP and oral dosing).

They are:

## HalfLife_Mod
This model calculates the half-life for a single subject from whom data was obtained throught the IV, IP, and oral routes of administration.
The distribution half-life, elimination half-life, partitioning of concentration between distribution and elimination, the volume of distribution, and the dose are assumed to be the same for all routes.
The sampling times are also assumed to be the same.

The likelihood calculations are done on the log(Concentration) scale, therefore the errors are assumed to be log-normal.

Model tested on fake data (as can be seen in the Simulations.Rmd file) and runs easily with no major issues (e.g. Rhats are 1, N_eff are
&gt;10% total samples, no divergent transitions, no max_treedepth warnings, recovers correct values).

## HalfLife_Mod_ML
This is a multilevel version of the previous model. All model parameters are considered multilevel.
The continuous positive parameters: volume of distribution, elimination half-life, IP and oral absorption half-lives are modeled as multilevel normal on the log scale.
The parameters constrained between 0 and 1: Absorbed fraction (IP and oral), partitioning between distribution and elimination, and the ratio of distribution half-life to elimination half-life are modeled as multilevel beta-distributed.

An issue with the model where some chains would reverse the elimination and distribution half-lives at the individual level (the means were ordered but the individual estimates were not) required a change in the model. In order to ensure that the distribution half-life is shorter than the elimination half-life, the distribution half-life is obtained by multiplying the elimination half-life by a number between 0 and 1, effectively `t_dist = prop * t_elim`. This proportion is modeled as beta-distributed and is applied to the actual elimination half-life. Both parameters are then transformed `log(2)/t` to keep the concentration calculations multiplicative.

There are some transformations from matrices to vectors that can probably be done away with somehow.
