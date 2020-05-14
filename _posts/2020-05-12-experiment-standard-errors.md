---
title: "Grouped data in A/B tests, redux: Clustered standard errors"
author: Kyle Carlson
layout: post
excerpt_separator: <!--more-->
---

*Use cluster-robust inference when your experiment randomizes by groups.*

![teaser]({{ site.url }}/images/power_by_randomization.svg){: .center-image width="80%"}
<!--more-->

Comments and feedback welcome on Twitter: [@KyleCSN](https://twitter.com/KyleCSN).

# Summary

This post assumes you are familiar with the [previous one]({{ site.url }}/experiment-panel-data/), in which we simulated models for grouped data in A/B tests. Today's post builds on those by considering grouped treatment assignment and clustered standard errors.

As before our setting is a photo-sharing website. Each person in the experiment can visit the site multiple times. Our experiment tests a change that we hope increases the probability of posting a photo. In the previous simulations we randomized at the **visit-level**. That is, on each visit we flipped a coin to decide whether to show the treatment or control version. However, in many situations we want to randomize at the **person-level**, flipping a coin for each person and putting them in a single version of the site for the entire experiment. Why? People may be confused or behave unnaturally if their experience on the site is inconsistent. Or, the functionality may be broken or incompatible between the two versions.[[^1]] We learn some key lessons about person-level randomization using simulations.

### Key lessons

1. Person-level randomization can substantially *decrease* statistical power relative to visit-level randomization. The power loss depends on the amount of between-person heterogeneity.
2. We need to adjust our inference to account for the person-level randomization. Otherwise, our p-values can be dramatically wrong. Fortunately, "clustered standard errors" handles this conveniently and generally. The correction is availabe in standard R and Python packages.

### Suggested readings

* "Nonstandard Standard Error Issues", chapter 8 of _Mostly Harmless Econometrics_ by Angrist and Pischke. [[pdf link](http://econ.lse.ac.uk/staff/spischke/mhe/ex_ch8.pdf)]

* "When Should You Adjust Standard Errors for Clustering?" by Abadie, Athey, Imbens, and Wooldridge. Stanford GSB working paper. [[pdf link]](https://www.gsb.stanford.edu/faculty-research/working-papers/when-should-you-adjust-standard-errors-clustering)


# Results

## Code and implementation

The [simulation notebook](https://colab.research.google.com/drive/1FxoZbFxknhXc4ISEvJBXkS80NONO1nW_?usp=sharing) is available on Colab. For more details on the models and data-generating process (DGP) see the previous post. This post uses the same benchmark DGP with adjustments as described below.

## Power loss from person-level randomization

To depict the power loss, we simulate the benchmark DGP under two randomization schemes: person-level and visit-level randomization (implemented by coinflips). There are 500 people in the experiment, each visiting the site exactly 8 times. Person-level randomization suffers when we have person-level heterogeneity. In this case, each person has a baseline propensity to post between 0.05 and 0.95. Recall from the last post our discussion of within-person and between-person variation. Due to the person-level randomization, we only have *between* variation and no way to difference out the heterogeneity. We cannot apply fixed/random effects.

The graph below shows simulated power for an effect of 0.04 and 5 percent significance level. The left bars show **visit**-level randomization. Analysis by a simple difference has power of about 70 percent. We can gain even more power (reaching over 80 percent) by using random effects. The right bar shows **person**-level randomization, which drops power to about 30 percent. (And we can't use random effects here.) The actual difference in power increases with (1) the number of visits per person, and (2) the degree of heterogeneity. In this scenario, by switching to person-level randomization we went from an appropriately powered experiment to a very underpowered one.

![power_by_randomization.svg]({{ site.url }}/images/power_by_randomization.svg){: .center-image width="100%"}

## Correct inference with person-level randomization

In many situations we have a strong reason to randomize by person anyway. In that case, we need to adjust our inference: standard errors, confidence intervals, and p-values. The figure shows what happens if we do _not_ adjust our standard errors and use the usual inference, for example, a chi-squared test, two-sample proportion z-test, or OLS with conventional standard errors. We display the results in terms of the type I error rate. That is, we simulate the benchmark DGP under the null hypothesis of no treatment effect. Each time we calculate the simple difference and conventional p-value to test for a difference. Then we track how often we get a p-value below 0.05. Nominally, this false positive rate should be 5 percent. However, the figure shows that the rate can be far above 0.05. When there is just one visit per person the rate is correct. But as the number of visits per person climbs the false positive rate increases rapidly, even passing 60 percent! This means we will be much more likely to *mistakenly* declare a "significant" treatment effect.

![coolplot]({{ site.url }}/images/fpr_by_group_size.svg){: .center-image width="100%"}

Fortunately there is a standard, simple correction called "clustered standard errors".


### Clustered standard errors

This is a general approach favored in applied econometrics that adjusts for clustering _and_ heteroscedasticity. For a good, but technical discussion, see ["When Should You Adjust Standard Errors for Clustering?" by Abadie, Athey, Imbens, and Wooldridge](https://economics.mit.edu/files/13927). The paper has three key lessons for us. First, when the experiment assignments are made at the group level, the conventional standard errors will be incorrect (page 12, corollary 1). Second, the clustered standard errors are correct so long as our sample has only a small portion of the persons that _could have_ appeared in the sample (page 12, corollary 2). Third, suppose the previous point is not true: Our sample has a large portion of the clusters that could be sampled. In other words, future samples will have substantial overlap in clusters. Under person-level randomization, the clustered standard errors will be conservative, and no better standard error estimator is available (page 13).

Standard stats packages allow clustering as an option that will update all regression results appropriately (standard errors, t-stats, and p-values) without any further manual calculations. In R the `sandwich` package does this. In Python `statsmodels` and `linearmodels` provide the correction. See the Colab notebook for example usages.


### Simulation results

To demonstrate the correction, we will return to the benchmark DGP simulations under the null hypothesis of no effect. Before we saw that with 8 visits per person, person-level randomization boosts the false positive rate to around 25 percent instead of the correct 5 percent. That result reappears in the figure below on the left side. The green point shows our grossly inflated error rate when using person-level randomization. However, when we use clustered standard errors the error rate is 5 percent as expected! The right side of the figure shows the error rates when using visit-level randomization, where both methods are correct.

![fpr_by_randomization]({{ site.url }}/images/fpr_by_randomization.svg){: .center-image width="100%"}

### More commentary on the Abadie et al. paper

This paper is well worth a read as it provides the most thorough framework for thinking about clustered standard errors. The technical framework has some unusual elements (for example, a sequence of populations). However, the key insights are available from the prose, proposition 1, and the two corollaries. 

The paper derives the approximate bias in the clustered standard errors (the difference from the true variance) as:

$$
\frac{\text{Proportion(Clusters sampled)} \cdot \text{Proportion(Units sampled)}}{\text{Total units}} \sum_{c=1}^\text{# clusters} (\text{Size of cluster $c$})^2 \cdot (\text{Effect heterogneity})^2.
$$
{: style="font-size:65%"}

The expression tells us that the clustered standard error estimator is approximately correct if either of the sampling proportions is small or there is no effect heterogeneity. Effect heterogeneity means the treatment has exactly the same effect on every unit, which is an unrealistic assumption in most cases. In our setting, Proportion(Units sampled) = 1 because we have data on every visit. So the remaining condition is that Proportion(Clusters sampled) is small. As discussed in the paper, the condition cannot be checked in the data. It depends on domain knowledge about your data-generating process. This proportion would be small if we expect future samples to have little overlap in clusters with our experiment sample.[[^20]] However, even if we have high overlap in our experiment, we still should use the clustered standard error estimator. When we have group-level assignments, there is no available improvement to the clustered standard errors. We can only improve if we introduce some within-cluster variation in treatment.

The other key derivation is the difference between the clustered estimator and the conventional estimator. In a typical DGP it is a sum of terms that look like

$$
\text{Effect heterogeneity}^2 + 4 \cdot (\text{Assignment clustering}) \cdot \text{Potential residuals}^2.
$$
{: style="font-size:85%"}

This result tells us that clusters standard errors will increase (relative to the conventional standard errors) with effect heterogeneity and assignment clustering. Our scenario of person-level randomization is the most extreme form of assignment clustering. 


# Conclusion

In practice, we are very often forced to put groups, or clusters, of data into the same treatment assignment. This is common when the group is a person using some software that we want to have consistent behavior. When we randomly assign the experiment treatment by group, we have two main complications. First, power decreases (in the likely case of heterogeneity between groups). So, if we can implement a within-person experiment, then we should! Second, we should use clustered standard errors to get correct inference. This correction is easily implemented in standard software packages, so there is no excuse for avoiding it!


# Appendix

## Two alternative approaches

Clustered standard errors is a general and simple approach to correct inference. However, there are other options you might consider.

* _Aggregate your data to the person-level_:  If you collapse the data, the usual analysis and inference results will be correct. This is the _between estimator_ discussed in the last post. The downside of this approach is that if the groups are of different sizes (likely to be skewed as discussed in the last post) then you need to implement some weighting to maintain power. Another disadvantage is that we cannot use visit-level covariates.

* _Moulton factor_: The Moulton factor (or "design effect") tells us how much the conventional standard errors need to be inflated to account for clustering. You can calculate the factor and then multiply the naive standard errors by it. However, the standard Moulton factor correction assumes a homoscedastic error structure, which is restrictive in many settings. For more details see chapter 8 of _Mostly Harmless Econometrics_ by Angrist and Pischke [[pdf link](http://econ.lse.ac.uk/staff/spischke/mhe/ex_ch8.pdf)].


## Notes

[^1]: Person- or user-level randomization is the default in many settings due to it being generally more conservative and a better approximation of the intended product experience. Within-person randomization is more likely in a research setting or when we are testing specific, narrow behavioral hypotheses. Within-group randomization is more of an option when the groups are machines and the units are some narrowly-scoped request, for example, if we are expertimenting with requests made between different machines.

[^20]: An example of *high overlap* would be a Facebook-wide experiment where users are clusters. Facebook would expect to have nearly the same sample of users in any experiment unless they specifically limited the experiment to only a small portion of users.
