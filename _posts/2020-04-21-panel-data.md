---
title: Panel data estimates in A/B tests
author: Kyle Carlson
layout: post
---


## Motivation

Randomized experiments (or A/B tests) often generate data with a natural heirarchy, for example, individual people that visit a website multiple times during the experiment. How can we analyze this experiment and be smart about boosting the precision of our results?

This post offers guidance on that scenario tailored for people that have the standard econometrics training. Such a person may want to use familiar, workhorse methods like the linear probability model and fixed effects. However, the standard training may not cover key complications in the A/B test setting, in particular, highly unbalanced panels and binary outcomes. 

There is hope. We can use our standard tools 

## Data-generating process

There are $$n$$ persons, each person $$i$$ having $$T_i$$ observations. Each observation can be a failure or success. There is an experimental treatment $$D_it$$ that increases the probability of success. The key interesting complication is that each person has an individual tendendency for a higher or lower success rate $c_i$. 

$$
P(Y_{it} = 1) = \alpha + d_{it} \beta + c_i
$$

$$
y_{it} = \alpha + d_{it} \beta + c_i + \epsilon_{it}
$$


$$
y_{it} = \alpha + d_{it} \beta + \nu_{it}
$$
where $$\nu_{it}=c_i + \epsilon_{it}$$.


## Technical detail

The number of persons is $$n$$. Each person $$i$$ has $$T_i$$ observations indexed by $$(i, t)$$. The total number of observations is $$N = \sum_i T_i$$. Each observation has a binary outcome $$Y_{it} \in \{0 (\text{failure}), 1 (\text{success})\}$$ and treatment indicator $$D_{it}$$. The treatment $$D_{it}$$ is assigned like a coin flip, independently of all other variables.

