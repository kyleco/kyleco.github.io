---
title: Boosting A/B test power with panel data models
author: Kyle Carlson
layout: post
excerpt_separator: <!--more-->
---

*You can boost statistical power by modeling the heterogeneity of your users.*

![coolplot]({{ site.url }}/images/coolplot.svg){: .center-image width="90%"}
<!--more-->

# DRAFT

In A/B testing we want to use methods with high statistical power because it accelerates our rate of experimentation and product improvement. This post explains how you can improve the power of your A/B tests using tools that should be familiar to anyone who has taken an econometrics course: ordinary regression, fixed effects, and random effects. These methods are all "correct" [[^1]], but the best one depends on the specifics of your data. We will use simulation and theory to learn several key lessons about these models. 

Where do these methods apply? Imagine we are experimenting on a website where people can post photos. During the experiment numerous people visit the website one or more times. We have two key factors. First, the observations are naturally **grouped** by person. We can count each individual visit or group our data at the person-day level. Second, we suspect that there is **heterogeneity**. In other words, some people love to post photos while others rarely do. Panel data models let us incorporate these factors to estimate the treatment effect more precisely, that is, with more statistical power.

However, we need to think carefully about applying these models because of two *practical complications* in our A/B test setting. First, the number of visits per person is likely to be very skewed with many people visiting only one time. Those people's data will be thrown away by the standard panel data model, fixed effects.  Second, in A/B tests we typically use a simplistic form of randomization, independent coin flips. Therefore, some people that visit multiple times will still see only the treatment or control experience. Fixed effects also throws away that data. We will better understand these limitations after learning about "between variation", "within variation", and the random effects model.

### Key lessons
1. The fixed effects model, although a workhorse in applied microeconomics with grouped data, can be far more *or less(!)* efficient than a simple regression. To choose between fixed effects and simple regression, you should consider two main factors: (1) the number of visits per person and (2) the variance of the individuals' propensity to post. These both increase the relative performance of fixed effects.
2. You can opt for the random effects estimator, which automatically adjusts for those factors and dominates both alternative models across a wide range of scenarios. 
3. But, if you include _observational_ covariates (as opposed to a randomized treatment) in your model, you should probably avoid random effects as the necessary assumptions are unlikely to be met. The relatively strong assumptions for random effects are the main reason economists favor fixed effects in observational work. This is very important to note but is covered in any econometrics text. 


### Code and implementation

The graphs and simulation results are available in this [Colab notebook](https://colab.research.google.com/drive/12mEJZsnVhBE7C0KC9QMQKGha5zY_loxf). The models are fit using the `linearmodels` package, but can also be cooked up with `statsmodels`. For implementation details, good sources include the `linearmodel` docs and source along with the [Stata doc for `xtreg`](https://www.stata.com/manuals13/xtxtreg.pdf).

## Introducing the models

Fixed effects and random effects are both ways of modeling unobserved heterogeneity. In our example scenario, this means each person has an individual propensity to post a photo, but we have no data directly telling us that propensity. Generically, the model looks like

$$
y_{it} = d_{it} \beta + c_i + \epsilon_{it}
$$

where $$i$$ indexes persons and $$t$$ indexes the visits of each person. The variable $$y_{it}$$ is a dummy indicating whether the person posted on that visit. The treatment dummy is $$d_{it}$$. The corresponding treatment effect is $$\beta$$ is our parameter of interest. It tells us how much the treatment version of the site boosts the probability of posting. Each individual's propensity to post is represented by $$c_i$$. Think of this as person $$i$$'s personal tendency to share photos.

The variation in the data can be split: between-person and within-person. Between-person variation refers to the fact that some persons post more than others and some persons are more exposed to the treatment than others. Within variation refers to the fact that *a given person* posts on some visits but not on others and sees the treatment version of the site on some visits and not on others.

#### Between-person and within-person variation: A Python demo
~~~
df[['i', 't', 'y', 'd']].head()
>   i  t  y  d
 0  0  0  0  1
 1  0  1  0  0
 2  1  0  0  0
 3  1  1  1  0
 4  1  2  0  0

# Between-person variation
df.groupby('i')[['y', 'd']].mean().std()
> y    0.404658
> d    0.372494

# Within-person variation
df.groupby('t')[['y', 'd']].transform(lambda x: x - x.mean()).std()
> y    0.483391
> d    0.489044
~~~

Given this underlying model and variation, let's consider four ways to estimate $$\beta$$. The key differences behind them are how they treat variation between and within persons.[[^9]]

For precise details on these models, see the section "Demonstrative implementations" in the [Colab notebook](https://colab.research.google.com/drive/12mEJZsnVhBE7C0KC9QMQKGha5zY_loxf). Each model from the `linearmodels` package is reimplemented manually with data transformations and OLS.

### Simple regression (pooled OLS)
Let's call this estimator $$\hat{\beta}_{\text{OLS}}$$. We can simply run a regression of $$y$$ on $$d$$. This is mathematically equivalent to comparing the averages of $$y$$ between the control and treatment groups. It combines both within and between variation. The regression we are fitting combines $$c_i + \epsilon_{it}$$ into a single error term: $$y_{it} = d_{it} \beta + \nu_{it}$$. Because we are ignoring the individual heterogeneity, it goes into our residuals and inflates their variance. In turn, the the variance of our estimator increases and power decreases.

### Fixed effects ("within estimator")
The intuition of fixed effects is that each individual is treated as their own control group, thus exploiting only the within variation. This makes each person's individual propensity irrelevant. We can implement the fixed effects estimator $$\hat{\beta}_{\text{FE}}$$ by substracting the person-specific averages from all of our data. We would regress $$y_{it} - \bar{y}_i$$ on $$d_{it} - \bar{d}_i$$. The "demeaning" also subtracts away the term $$c_i$$. This _potentially_ reduces the residual variance and increases precision.

For fixed effects to work, some individuals must see both the control and treatment sites. In our experiment scenario we face the problem that some individuals may see only one variant. That happens if they visit just one time or happen to get the same coin flip on every visit. These persons have no within-person variation in the treatment $$d_{it}$$! The fixed effects estimator ignores these individuals, which decreases our effective sample size.

### Between estimator
The between estimator $$\hat{\beta}_{\text{BE}}$$ is the complement of the within estimator. It uses only the variation between individuals and discards the within-person variation. We can implement it by averaging all our data to the person-level and then running a regression. That is, we fit the regression $$\bar{y}_i = \alpha + \bar{d}_i \beta + \bar{\nu}_i$$. For this model to work, we must have variation in $$\bar{d}_i$$. In proportion of visits to the treatment site must be higher for some persons than others. That will happen as a consequence of the control/treatment assignment being made by independent coinflips, generating a mixture of binomial ditributions. The between estimator is rarely used in practice, but it is an ingredient in the random effects estimator.

### Random effects
We said that the fixed effects and between estimators use different sources of variation in the data to estimate the treatment effect. What if we could combine both to get a better estimate? This is what random effects does! In fact, the random effects estimator is an average of the fixed effects and between estimators:[[^14]]

<!-- . The average uses a weighting matrix $$A(\hat{\omega})$$ which adapts to the relative amounts of between and within variation and number of observations per group: -->

$$
\hat{\beta}_{\text{RE}} = \text{WeightedAverage}(\hat{\beta}_{\text{FE}}, \hat{\beta}_{\text{BE}})
$$

<!-- A(\hat{\omega}) \hat{\beta}_{\text{FE}} + (I - A(\hat{\omega}))\hat{\beta}_{\text{BE}} -->

The weighting of the average adapts to the relative amounts of between and within variation and number of observations per group. The key weighting parameter[[^11]] is: 

$$
\hat{\omega} = \tfrac{\hat{\sigma}_{\epsilon}}{\sqrt{T\hat{\sigma}^2_{c} + \hat{\sigma}^2_{\epsilon}}} = \tfrac{\text{Within residual variation}}{\sqrt{\text{Number of visits per person}\times \text{Heterogeneity} + \text{Within residual variance}}}.
$$

The parameter $$\hat{\omega}$$ controls how much of the within and between variation are used by $$\hat{\beta}_{\text{RE}}.$$ (Think [inverse-variance weighting!](https://en.wikipedia.org/wiki/Inverse-variance_weighting)) As $$\hat{\omega}\rightarrow 1$$, the random effects estimator approaches the between estimator. But, as $$\hat{\omega}\rightarrow 0$$, the random effects estimator approaches the fixed effects estimator. To make $$\hat{\omega}$$ small, we need to have a high number of visits per person ($$T$$) or large heterogeneity between people ($$\hat{\sigma}^2_{c}$$). Intuitively, if there is a lot of heterogeneity between people, we should weight more heavily the fixed effects estimator, which subtracts away that heterogeneity.  

The downside of the random effects estimator, and why it is used far less frequently than fixed effects, is that it requires a very strong exogeneity assumption. In particular, the regressors must be unrelated to the individual heterogeneity. For an A/B test, $$d_{it}$$ is an independent coinflip, so it is independent of $$c_i$$. That's great, but we will probably be in trouble if we want to include any other covariates in the model. In that case, fixed effects will be preferrable.

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

### Individual heterogeneity

What happens if we adjust the variance of $$c_i$$? In the first panel below we have three example distributions of $$c_i$$ showing low, medium, and high heterogeneity. The second panel shows the precision of the estimators as the heterogeneity ranges from low to high. At low levels of heterogeneity the standard error of the fixed effects estimator is much larger than the other estimators. However, as the heterogneity increases both the fixed effects and random effects estimators increase greatly in precision, while the simple regression has no change in performance.[[^10]]

![se_by_sigma_c]({{ site.url }}/images/se_by_sigma_c.svg){: .center-image width="100%"}

You can estimate the heterogeneity after a random effects model using the `variance_decomposition` property. The results would look something like this. The value after "Effects" is $$\hat{\sigma}^2_c$$.
```python
r = RandomEffects(y, exog).fit()
print(r.variance_decomposition)

Effects                   0.168079
Residual                  0.086765
Percent due to Effects    0.659536
Name: Variance Decomposition, dtype: float64
```

### Visits per person

The other factor of interest is the number of visits per person. Below we vary the average number of visits from 2 to 33. The first panel again shows example distributions, one with a small number of visits per person and the other with a larger number. The second panel reports the efficiency of each estimator relative[[^2]] to the random effects estimator, in particular, the "Fixed effects" plot is $$\tfrac{\text{Var}(\hat{\beta}_\text{RE})}{\text{Var}(\hat{\beta}_\text{FE})}$$. 

![se_by_group_size]({{ site.url }}/images/se_by_group_size.svg){: .center-image width="100%"}


## Conclusion

When our experiment has grouped data and heterogeneity, we can increase our statistical power by applying the standard panel data models from our econometrics training.[[^3]] The familiar fixed effects model can help or hurt significantly. To understand that we developed simulations and intuition about the key factors: group size and degree of heterogeneity. We also saw that the random effects model automatically adapts based on those factors, in effect, making the choice for us.

This post only scratches the surface of these types of models. Practitioners should use simulations with realistic data-generating processes inspired by their data, rather than the stylized, toy processes here. We also made simplifications, for example, choosing a constant additive treatment effect rather than a heterogeneous treatment effect (that could also be correlated with the heterogeneity in intercepts!). Finally, we also ignored the complicated matter of estimating of standard errors. These matters are left to future posts.

## Notes

[^1]: By "correct" we mean that they give us consistent estimates of the treatment effect.

[^9]: A comprehensive reference for these models is *Econometric Analysis of Cross Section and Panel Data, Second Edition* (2010) by Jeffrey M. Wooldridge. See also *An Introduction to Classical Econometric Theory* (2000) by Paul A. Ruud for more depth on random effects.

[^10]: It may be surprising that adjusting the heterogeneity has no effect on the pooled OLS estimator. Adding variation *should* increase the variance of the estimator. However, since this is a linear probability model that combined residual variance in constant. Adjusting the heterogeneity simply apportions variance between the individual effects and the indiosyncratic effects.

[^11]: The formula for $$\hat{\omega}$$ is simplified for presentation by assuming $$T_i=T$$, i.e., that all persons visited the same number of times (a balanced panel). 

[^2]: We report relative efficiency because in this case it makes the pattern easier to see than reporting the raw standard errors.

[^3]: In other contexts these models are called hierarchical models or multilevel models.

[^14]: For more details on the math of the RE estimator see Ruud (2000) mentioned above or these [lecture notes from James Powell at Berkeley](https://eml.berkeley.edu/~powell/e240b_sp10/pdnotes.pdf). 

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
