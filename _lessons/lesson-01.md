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

The main idea of the potential outcomes model is that each individual has two potential outcomes, i.e., outcomes that would obtain under different states of the treatment of interest. For example, suppose we are looking at individuals using a search engine and that the treatment is seeing a display ad for [UNICEF](https://www.unicef.org/). We are interested in whether the ad causes individuals to donate money to UNICEF. In the potential outcomes model each individual has two donation outcomes: one in the theoretical world in which they saw the ad and another in the world in which they did not. The ad exposure corresponds to a "treatment" state and the lack of ad to a "control" state.

To analyze this problem rigorously we can use a formal, mathematical model. We will index individuals by $$i$$. A given individual $$i$$ has the outcomes $$y_i^0$$ and $$y^1_i$$, corresponding to the control state and treatment state, respectively. The $$y$$ values represent how much money $$i$$ donates:

- $$y^0_i$$ = donation amount in the control state (no ad),
- $$y^1_i$$ = donation amount in the treatment state (saw ad).

For each individual $$i$$ we will suppose that there is only one instant in which they can be exposed to the ad and make a donation decision. Therefore, we can only observe, in our data, one of $$y^0_i$$ or $$y^1_i$$, not both. If the actual treatment state is $$d_i \in {0, 1}$$, then the observed outcome is $$y_i = y^1_i d_i + y^0_1 (1 - d_i) = y^{d_i}_i$$.

We want to estimate causal effects. The basic units of causality are the individual-level treatment effects $$y^1_i - y^0_i$$, which represent the difference in donation amount caused by exposure to the ad. However, it is impossible to directly calculate this treatment effect from data because one of the values is always missing from out data! This limitation is the *fundamental problem of causal inference*. We will return to this problem, but we are not yet done building up our framework. It needs to be embedded in a probability model.

### Example data from UNICEF ads problem
The table below shows the potential donation amounts for four individuals. Such a data set can only be obtained by an oracle that knows what each individual will do under both conditions. In practice, we non-superhuman economists can only obtain the first 3 columns of data.

| Individual $$(i)$$ | Actually exposed to ad? $$(d_i)$$ | Actual donation amount $$(y_i)$$ | Potential donation amount:<br> No ad $$(y^0_i)$$ | Potential donation amount:<br> Exposed to ad $$(y^1_i)$$ | Individual treatment effect $$(d_i)$$
|-------|--------|---------|
| 1 | 1 (Yes) | $0  | $0  | $0    | 0
| 2 | 0 (No)  | $0  | $0  | $5    | 5
| 3 | 1 (Yes) | $10 | $10 | $10   | 10
| 4 | 1 (Yes) | $10 | $5  | $10   | 5
| 5 | 0 (No)  | $5  | $5  | $0    | -5

One could calculate the average actual donation amount for the exposed (\~$6.67) and non-exposed ($2.50) groups. The difference between these is \~$4.17. Is this number a good representation of the causual effect of the ads? One might say "no" and note that a sample size of 5 is very small. Although sample size is a concern, this problem is not the fundamental one at the moment. Imagine the data set is expanded to an abritrarily large sample size: millions, billions, or more. Now we can put aside our concerns about sample size. This is the study of *identification*, what we can learn from an arbitarily large quantity of data. If we imagine our data as realizations of random variables, then the average of any variable in the sample will be close to the *expectation* of the random variable. We know this because of the law of large numbers. Now we can work in terms of the random variables, which will make our analysis tractable.[^learning_from_data]

### A probability model for potential outcomes

To make progress, lets be specific about building probability into the potential outcomes model. Suppose that $$y^0_i$$ and $$y^1_i$$ are realizations of the random variables $$Y^0$$ and $$Y^1$$. The treatment state is commonly representated by a random variable $$D$$. The observed outcome is $$Y_i = Y^1_i D_i + Y^0_1 (1 - D_i)$$. (Note: This follows the convention of using upper case characters to represent random variables and lower case to represent realizations of the variables.)

Framing this as a probability model gives us all the tools of probability and statistics. (This abstraction also reduces the number of objects to work with. Instead of $$O(N)$$ data points there are a handful of distributions.) While the individual treatments effects $$y^1_i - y^0_i$$ cannot be calculated in practice, we do have hope of calculating the *average treatment effect* (ATE) $$E[\delta] = E[Y^1 - Y^0]$$, where $$E$$ is the expectation operator. Our hope comes from statistics, which gives us tools for relating data to probability models. In causal inference we typically want to make computations on our data $${(d_i, y_i): i=1...N}$$ to estimate the parameter ATE of our probability model.

This particular construction of the probability model warrants few comments about why it is used and what it implies for our analysis. Other constructions exist, but this one is common in econometrics and many other disciplines. This approach is also implicit in many industrial applications of causal inference, for example, conventional A/B testing approaches. 

Our framework has some unintuitive interpretations. A typical introductory statistics course will use a framework with a "population" and a "sample". For example, a population might be the 327 million individuals in the United States. A sample might be a random selection of 1000 of those individuals whose data is collected as part of a research survey. A statistical problem would then be using the average age among the sample ("sample mean") to estimate the average age in the population ("population mean"). However, in our construction there is no requirement that our data "sample" actually be a subset of any well-defined population. In applications we may have *all* of the data. Many studies have all of the data in a relevant class, for example, all births in the United States (CDC Vital Statistics birth certificates data), all deaths in the United States (CDC death certificate data), or all people in the United States (U.S. Census data). Similarly, an internet business will typically have data about *all* of the people that visited the web site, not a sample. In these cases, the conventional approach is to adopt the useful fiction that the data were drawn from an imaginary "superpopulation". The variables $$(Y, Y^0, Y^1, D)$$ characterize the superpopulation. Why is it useful to learn about an imaginary population? The answer is fairly simple in the case of industrial applications. Suppose our search engine conducted an A/B test on all of the visitors in the past month and estimated the effect of the UNICEF advertisement. If we imagine each future month as a new random sample drawn from the superpopulation, we can generalize our learnings from the A/B test sample to future visitors to the search engine. Note that without assuming some commonality between past and future our analysis of the data will be useless for future decisions. The assumption of a shared superpopulation is perhaps the simplest useful form of commonality.[[^hume_causality]]

<!--  -->



> For now the interest is in *identification* of treatment effects, which mean assume an infinite quantity of data.

| Effect name | Quantity
|-------|-------|
| Average Treatment Effect (ATE) | $$E[Y^1 - Y^0]$$
| Average Treatment Effect on the Treated (ATT) | $$E[Y^1 - Y^0 \| D=1]$$
| Average Treatment Effect on the Un-treated (ATU) | $$E[Y^1 - Y^0 \| D=0]$$
| Naive Average Treatment Effect (NATE) | $$E[Y^1 \| D=1] - E[Y^0 \| D=0]$$




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


# Notes

[^learning_from_data]: This argument has parallels in *[Learning From Data](https://www.amazon.com/gp/product/1600490069)* (2012), the book on machine learning fundamentals by Abu-Mostafa, Magdon-Ismail, and Lin. Section 1.3 "Is Learning Feasible?" discusses the possibility of learning about an *unknown* target function from data. The way to make this feasible is to use probability.

[^hume_causality]: For a classic philosophical take on the problem of learning from past experience to understand the future, start with ["Kant and Hume on Causality"](https://plato.stanford.edu/entries/kant-hume-causality/) in the Standford Encyclopedia of Philosphy. This topic is out of scope for this course.
