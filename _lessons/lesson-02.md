---
title: Lesson 2 - Uncertainty
author: Kyle Carlson
layout: default
---
{:auto_ids}

**Table of contents** 
* Table of contents
{:toc}


## Uncertainty and variation

In the previous lesson we assumed an infinite quantity of data, which removed sampling variation and simplified our analysis of average treatment effects. However, in any actual experiment there is a limited quantity of data, so we must face the complication of uncertainty.

## Random samples

A simple data set would look like the following table. Here $$N$$ is the _sample size_, that is, the number of individuals in the sample. A more compact way to represent the data set is $$\{(y_i, d_i): i=1,\dots,N\}$$. Under a random sampling assumption, each data point $$(y_i, d_i)$$ is an independent realization of the superpopulation variables $$(Y, D)$$.

|Outcome | Treatment status
|-----|----
| $$y_1$$ | $$d_1$$
| $$y_2$$ | $$d_2$$
| $$\ldots$$ | $$\ldots$$
| $$y_N$$ | $$d_N$$




## Point estimators

$$\bar{y}$$ is our estimator of $$E[Y]$$.

Unbiasedness: $$E[\bar{y}] = E[Y]$$

Standard error: $$V[\bar{y}] = \tfrac{\sigma_Y^2}{N}$$.

