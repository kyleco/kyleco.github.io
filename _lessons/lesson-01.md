---
title: Lesson 1 - Causality Fundamentals
author: Kyle Carlson
layout: post
---
{:auto_ids}

**Table of contents** 
* Table of contents
{:toc}


## The importance of causality

In the context of business decisions and actions, people often speak of "pulling levers". This idiom is a colloquial way of refering to causality. It highlights the importance of causality. The people in the business want to take some action that will have a causal effect on some aspect of the business. Example questions include:

* If our social media site buys Google search ads, how much will it increase our monthly visitors?
* If our insurance company switches our actuarial model from a logistic regression to a gradient boosted decision tree, how much will our annual claims payouts decrease?
* If our courier company gives a 25% wage increase to bike messengers, how many more hours will they work per week?
* If our online banking site implements a CAPTCHA for new accounts, will our conversion rate fall by less than 1 percent?
* If our hotel increases prices by 10 percent, how much will monthly booking revenue increase?


## What is causality?

The definition of causality is debated by statisticians, economists, computer scientistis, and philosophers. We are in an economics course, so we will follow the conceptual framework most popular with economists, the potential outcomes model, also known as the (Neyman-)Rubin causal model. This framework underlies thousands of observational studies and experiments in economics, political science, and sociology.

## Potential outcomes model

The main idea of the potential outcomes model is that each individual has two potential outcomes, i.e., outcomes that would obtain under different states of the treatment of interest. For example, suppose we are looking at individuals using a search engine and that the treatment is seeing a display ad for [UNICEF](https://www.unicef.org/). We are interested in whether the ad causes individuals to donate money to UNICEF. In the potential outcomes model each individual has two donation outcomes: one in the theoretical world in which they saw the ad and another in the world in which they did not. The ad exposure corresponds to a "treatment" state and the lack of add to a "control" state.

To put this in mathematical notation we will index individuals by $$i$$. A given individual $$i$$ has the outcomes $$y_i^0$$ and $$y^1_i$$, corresponding to the control state and treatment state, respectively. The $$y$$ values represent how much money $$i$$ donates:

- $$y^0_i$$ = donation amount in the control state (no ad),
- $$y^1_i$$ = donation amount in the treatment state (saw ad).

For each individual $$i$$ we will suppose that there is only one instant in which they can be exposed to the ad and make a donation decision. Therefore, we can only observe, in our data, one of $$y^0_i$$ or $$y^1_i$$, not both. If the actual treatment state is $$d_i \in {0, 1}$$, then the observed outcome is $$y_i = y^1_i d_i + y^0_1 (1 - d_i) = y^{d_i}_i$$.

We want to estimate causal effects. Typically we start with the individual-level treatment effect $$y^1_i - y^0_i$$, which represents the difference in donation amount caused by exposure to the ad. It is impossible to directly calculate this treatment effect from data because one of the values is always missing from out data! 

Does this mean causal inference is hopeless? No, but to make progress we need to develop a probabilistic version of the potential outcomes model. Suppose that $$y^0_i$$ and $$y^1_i$$ are realizations of the random variables $$Y^0$$ and $$Y^1$$. The treatment state is commonly representated by a random variable $$D$$. The observed outcome is $$Y_i = Y^1_i D_i + Y^0_1 (1 - D_i)$$.

Framing this as a probability model gives us all the tools of probability and statistics. While the individual treatments effects $$y^1_i - y^0_i$$ cannot be calculated, we do have hope of calculating the *average treatment effect* (ATE) $$E[\delta] = E[Y^1 - Y^0]$$, where $$E$$ is the expectation operator. Our hope comes from statistics, which tells us that estimating the ATE with data is feasible under the right conditions.


> For now the interest is in *identification* of treatment effects, which mean assume an infinite quantity of data.




The average treatment effect is $$ \mathbb{E} [Y(1) - Y(0)] $$.


## Example: Vacation cabin bookings


| Season | Room | Counterfactual:<br>Low price | Counterfactual:<br>High price
|-------|--------|---------|
| Summer | A | Booked | Booked
| Summer | B | Booked | Booked
| Summer | C | Booked | Booked
| Summer | D | Booked | Vacant
| Winter | A | Booked | Booked
| Winter | B | Booked | Booked
| Winter | C | Booked | Vacant
| Winter | D | Vacant | Vacant

