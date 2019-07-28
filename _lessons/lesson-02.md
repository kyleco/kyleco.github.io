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

A simple data set would look like the following table. Here $$N$$ is the _sample size_, that is, the number of individuals in the sample. A more compact way to represent the data set is $$\{(y_i, d_i, \mathbf{x}_i): i=1,\dots,N\}$$. Under a random sampling assumption, each data point $$(y_i, d_i, \mathbf{x}_i)$$ is an independent realization of the superpopulation variables $$(Y, D, \mathbf{X})$$. Note that we use lowercase symbols to refer to a specific realization of fixed values. When we want to treat the data as being random we use uppercase: $$\{(Y_i, D_i, \mathbf{X}_i): i=1,\dots,N\}$$.

|Outcome | Treatment status | Covariates
|-----|----
| $$y_1$$ | $$d_1$$ | $$\mathbf{x}_1$$
| $$y_2$$ | $$d_2$$ | $$\mathbf{x}_2$$
| $$\ldots$$ | $$\ldots$$| $$\ldots$$
| $$y_N$$ | $$d_N$$  | $$\mathbf{x}_N$$


> Important terminological point to avoid confusion: Each $$(y_i, d_i, \mathbf{x}_i)$$ is **not** a sample. It is an _observation_, _unit_, or _individual_. The data set $$\{(y_i, d_i, \mathbf{x}_i): i=1,\dots,N\}$$ does **not** contain "N samples". The entire data set is a single sample with a size of $$N$$. This distinction is important for understanding sampling distributions, especially in more complex cases.  

## Point estimators

_Estimators_ are functions of random variables that we use to estimate a population parameter. For example, if we want to estimate the population parameter $$E[Y]$$ a natural estimator is the sample mean of $$Y_i$$. Conventionally, we use a bar symbol to denote the sample mean, so $$\bar{Y}=\tfrac{1}{N}\sum_{i=1,\ldots,N}{Y_i}$$. Note that we used uppercase because estimators are functions of _random_ variables. Therefore, estimators are also themselves random variables with distributions, which we study to know how well the estimator performs. When we think of a particular, fixed sample of data the corresponding quantity $$\bar{y}=\tfrac{1}{N}\sum_{i=1,\ldots,N}{y_i}$$ is called an _estimate_ and has a fixed value. 

> Another way to think of this: An **estimator** is a **procedure**, and an **estimate** is the **result** of the procedure. 



$$\bar{y}$$ is our estimator of $$E[Y]$$.

Unbiasedness: $$E[\bar{y}] = E[Y]$$

Standard error: $$V[\bar{y}] = \tfrac{\sigma_Y^2}{N}$$.

