---
title: Boosting A/B test power with panel data models
author: Kyle Carlson
layout: post
---

In A/B testing we want to use methods with high statistical power because it accelerates our rate of experimentation and product improvement. This post explains how you can improve the power of your A/B tests using tools that should be familiar to anyone who has taken an econometrics course: ordinary regression, fixed effects, and random effects model. These methods are all "correct", but the best one depends on the specifics of your data. We will use simulation to learn several key lessons about these models. 

Where do these methods apply? Imagine we are experimenting on a website where people can post messages. During the experiment numerous people visit the website one or more times. We have two key factors. First, the observations are naturally grouped by person. We can count each individual visit or group our data at the person-day level. Second, we suspect that each person has an individual propensity to post. Panel data models let us incorporate these factors to estimate the treatment effect more precisely, that is, with more statistical power.

### Key lessons
1. The fixed effects model, although a workhorse in applied microeconomics with grouped data, can be far more *or less* efficient than a simple regression. To choose between fixed effects or simple regression, you should consider two main factors: (1) the number of visits per person and (2) the variance of the individuals' propensity to post. These both increase the relative performance of fixed effects.
2. You can opt for the random effects estimator, which automatically adjusts for those factors and dominates both alternative models across a wide range of scenarios. 
3. But, if you include covariates in your model, you should probably avoid random effects as the necessary assumptions are unlikely to be met.

## Introducing the models

Fixed effects and random effects are both ways of modeling unobserved heterogeneity. In our example scenario, this means each person has an individual propensity to post a message, but we have no data directly telling us that propensity. Generically, the model looks like

$$
y_{it} = \alpha + d_{it} \beta + c_i + \epsilon_{it}
$$

where $$i$$ indexes persons and $$t$$ indexes the visits of each person. The variable $$y_{it}$$ is a dummy indicating whether the person posted on that visit. The treatment dummy is $$d_{it}$$. The corresponding treatment effect is $$\beta$$ is our parameter of interest. It tells us how much the treatment version of the site boosts the probability of posting. Each individual's propensity to post is represented by $$c_i$$. Think of this as person $$i$$'s baseline tendency.

Given this underlying model, let's consider three ways to estimate $$\beta$$. 

### Simple regression (pooled OLS)
Let's call this estimator $$\hat{\beta}_{\text{OLS}}$$. We can simply run a regression of $$y$$ on $$d$$. This is mathematically equivalent to comparing the averages of $$y$$ between the control and treatment groups. The regression we are fitting combines $$c_i + \epsilon_{it}$$ into a single error term: $$y_{it} = \alpha + d_{it} \beta + \nu_{it}$$. Because we are ignoring the individual heterogeneity, it goes into our residuals and inflates their variance. That increases the variance of our estimator.

### Fixed effects ("within estimator")
The intuition of fixed effects is that each individual is treated as their own control group. This makes each person's individual propensity irrelevant. We can implement the fixed effects estimator $$\hat{\beta}_{\text{FE}}$$ by substracting the person-specific averages from all of our data. We would regress $$y_{it} - \bar{y}_i$$ on $$d_{it} - \bar{d}_i$$. The "demeaning" also subtracts away the term $$c_i$$. This _potentially_ reduces the residual variance and increases precision.

For the idea of fixed effects to work, some individuals must see both the control and treatment sites. In our experiment scenario we face the problem that some individuals may see only one variant. That happens if they visit just one time or happen to get the same coin flip on every visit. The fixed effects estimator ignores these individuals, which decreases our effective sample size.

### Between estimator
The between estimator $$\hat{\beta}_{\text{BE}}$$ is the complement of the within estimator. It uses only the variation between individuals and discards the within-person variation. We can implement it by averaging all our data to the person-level and then running a regression. That is, we fit the regression $$\bar{y}_i = \alpha + \bar{d}_i \beta + \bar{\nu}_i$$. For this model to work, we must have variation in $$\bar{d}_i$$, the higher proportion of visits to the control site. That will happen as a consequence of the control/treatment assignment being made by independent coinflips.

### Random effects

We said that the fixed effects and between estimators use different sources of variation in the data to estimate the treatment effect. What if we could combine both to get a better estimate? This is what random effects does! In fact, the random effects estimator is an average of the fixed effects and between estimators. The average uses a weighting matrix $$A(\hat{\omega})$$ which adapts to the relative amounts of between and within variation and number of observations per group:

$$
\hat{\beta}_{\text{RE}} = A(\hat{\omega}) \hat{\beta}_{\text{FE}} + (I - A(\hat{\omega}))\hat{\beta}_{\text{BE}}
$$

The key parameter is 

$$
\hat{\omega} = \tfrac{\hat{\sigma}_{\epsilon}}{\sqrt{T\hat{\sigma}^2_{c} + \hat{\sigma}^2_{\epsilon}}} = \tfrac{\text{Within residual variation}}{\sqrt{\text{Number of visits per person}\times \text{Heterogeneity} + \text{Within residual variance}}}.
$$

The parameter $$\hat{\omega}$$ controls how much of the within and between variation are used by $$\hat{\beta}_{\text{RE}}$$. As $$\hat{\omega}\rightarrow 1$$, then the random effects estimator approaches the between estimator. But, as $$\hat{\omega}\rightarrow 0$$, then the random effects estimator approaches the fixed effects estimator. To make $$\hat{\omega}$$ small, we need to have a high number of visits per person ($$T$$) or large heterogeneity between people ($$\hat{\sigma}^2_{c}$$).   


## Simulations

To see these models in action, we will run Monte Carlo simulations of thousands experiments. On each "experiment" data set we will fit each of the models to generate the sampling distributions of $$\hat{\beta}_{\text{OLS}}, \hat{\beta}_{\text{FE}},$$ and $$\hat{\beta}_{\text{RE}}$$. Our outcome of interest is the standard deviation of each estimator, that is, the true standard error. 

The data-generating process in the simulations uses the linear probability model

$$
P(Y_{it} = 1) = d_{it} \beta + c_i
$$

where
1. There are 100 person, $$i \in \{1,2,3,\ldots,100\}$$,
2. The distribution of visits per person has as mixture distribution: $$T_i \sim \text{Poisson}(\mu_i), \mu_i \sim \text{Exponential}(\lambda)$$,
3. The treatment is assigned by coinflip on each visit, $$d_{it} \sim \text{Bernoulli}(0.5)$$,
4. There is a constant additive treatment effect $$\beta = 0.04$$, and
5. The individual propensities to post are distributed symmetrically, $$c_i \sim \text{Beta}(\alpha, \alpha)$$ rescaled s.t. $$c_i \in [0.05, 0.95]$$. 

<!-- - $$\beta = 0.04$$
- $$c_i \sym \text{Beta}(\alpha, \alpha)\text{scaled between 0.05 and 0.95}$$
- $$i \in 1,2,3,\ldots,100$$
- $$T_i \sym \text{Poisson}(\mu_i), \lambda\sym\text{Exponential}(\lambda)$$. -->
<!-- #### Simple regression (pooled OLS) -->

### Individual heterogeneity

What happens if we adjust the variance of $$c_i$$? In the first panel below we have three example distributions of $$c_i$$ showing low, medium, and high heterogeneity. The second panel shows the precision of the estimators as the heterogeneity ranges from low to high. At low levels of heterogeneity the standard error of the fixed effects estimator is far higher than the other estimators. However, as the heterogneity increases both the fixed effects and random effects estimators increase greatly in precision, while the simple regression has no increase in performance.

![se_by_sigma_c]({{ site.url }}/images/se_by_sigma_c.svg){: .center-image width="100%"}

### Visits per person

The other factor of interest is the number of visits per person. Below we vary the average number of visits from 1 to 33. The first panel again shows example distributions, one with a small number of visits per person and the other with a larger number. The second panel reports the efficiency of each estimator relative to the random effects estimator, in particular, the "Fixed effects" plot is $$\tfrac{\text{Var}(\hat{\beta}_\text{RE})}{\text{Var}(\hat{\beta}_\text{FE})}$$. 

![se_by_group_size]({{ site.url }}/images/se_by_group_size.svg){: .center-image width="100%"}



<!-- ![heterogeneity]({{ site.url }}/images/heterogeneity.svg){: .center-image width="100%"}
![group_size]({{ site.url }}/images/group_size.svg){: .center-image width="100%"} -->

 <!-- Many discussions of random effects and fixed effects models focus on whether the individual effects should be viewed as fixed parameters or random variables. Additionally, econometrics textbooks place the most emphasis on the relatively strong assumptions needed for the random effects estimator to be consistent. Neither of these are our concern here. Instead we will view the random effects estimator as a way to combine the two forms of variation: between-persons and within-persons. -->

<!-- On each visit the web site flips a coin and shows the old version of the site (control) or the new one (treatment). We hope the new version increases the probability of a post on each visit. -->

<!-- 
## Key lessons
- Pooled OLS, fixed effects, and random effects all consistently estimate the treatment effect in a randomized experiment. Textbook treatments focus on the conditions for consistency but we do not need to worry about this. Our choice can be driven by efficiency and practicality. 
- In randomized experiments (A/B tests) the distribution of group sizes can be very skewed and there can be substantial heterogeneity between groups. These influence the relative performance of estimators. Fortunately, in many settings an experimenter can use previous data to know which model will perform best.
- Recommendations: The random effects estimator is generally more efficient than fixed effects or OLS in this setting. However, experiments that want to use pooled OLS or FE should carefully analyze the distribution of intercepts and the number of observations per group to determine which is more efficient. When the group sizes are large FE is nearly as efficient as RE and may be preferrable if covariates are to be used.

### More details
- The fixed effects estimator can be substantially more or less efficient than pooled OLS depending on the DGP.
- The relative efficiency of FEs increases with the number of observations per group and the variance of the group intercepts.
- For our benchmark distribution of intercepts, FE is more efficient than pooled when the average group size is above X. However, as the proportion of singleton groups approaches 0 the FE estimator approaches non-identifiability because all within-group variability is lost.
- For our benchmark group size, FE is more efficient when the variance is intercepts is above Y. For extreme distributions of intercepts, FE can be much more efficient than pooled.
- The random effects estimator is more efficient than the alternatives across a wide range of data-generating processes. It correctly handles the DGPs that cause FE or pooled OLS to perfom very poorly.


### Other lessons
- Standard errors should be adjusted for clustering by group and heteroskedasticity.
- Estimated standard errors accurately reflect the precision of each estimator, and can practically be used to choose between fixed effects and pooled OLS.
- If block randomization by group can be implemented the efficiency of FEs will increase as variation will be concentrated within groups rather than between.

## Motivation

Randomized experiments (or A/B tests) often generate data with a natural heirarchy, for example, individual people that visit a website multiple times during the experiment. How can we analyze this experiment and be smart about boosting the precision of our results?

This post offers guidance on that scenario tailored for people that have the standard econometrics training. Such a person may want to use familiar, workhorse methods like the linear probability model and fixed effects. However, the standard training may not cover key complications in the A/B test setting, in particular, highly unbalanced panels and binary outcomes. 

There is hope. We can use our standard tools 

## Data-generating process

There are $$n$$ persons, each person $$i$$ having $$T_i$$ observations. Each observation can be a failure or success. There is an experimental treatment $$D_it$$ that increases the probability of success. The key interesting complication is that each person has an individual tendendency for a higher or lower success rate $$c_i$$. 

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
 -->
