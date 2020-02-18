---
title: Lesson 4 - Treatment compliance
author: Kyle Carlson
layout: post
---
{:auto_ids}

**Table of contents** 
* Table of contents
{:toc}

# Key terms

Compliance refers to the question of whether the designed treatment groups in the experiment correspond to actual treatment states. Key terms in the study of compliance.

- The _assigned treatment_ is what the experiment designer _intends_ a subject to receive based on randomization.
- The _actual treatment_ is the treatment the subject actually receives or experiences.
- _Noncompliance_ refers to cases where the actual treatment does not match the assigned treatment. Subjects in the control group may accidentally receive the treatment. Alternatively, subjects in the treatment group may fail to receive the treatment.
- _One-sided noncompliance_ means that only one of the groups in the experiment has noncompliance. Usually we consider the case that some subjects in the treatment group fail to actually receive the treatment.

# Examples of noncompliance

- In a clinical study of a new heart disease drug, 50 percent of patients are randomly assigned to receive the new drug. However, some in the treatment group are nervous about potential side effects and secretly do not take the drug.
- In another study of anxiety treatment, the control group is assigned to take a standard anxiolytic drug, and the treatment group is assigned ketamine. However, some people in the control group take ketamine bought on the internet.
- Political scientists are studying the effect of door-to-door messages on voter turnout. A sample of registered voters is identified and 50 percent are randomly assigned to have a canvasser visit their house to deliver a face-to-face message about the political issues. Those in the control group are left alone. However, 20 percent of the people in the treatment group do not receive the message because they are not home when the canvasser visits their house.
- Researchers are studying child mortality in 50 rural villages in Indonesia. Half of the villages are randomly assigned to receive vitamin A supplements. However, a typhoon washes out the roads in one district which prevents 6 villages from receiving the supplement shipment.
- Delyverizely is A/B testing a new version of their Android mobile app. Half of the current users are randomly assigned to get the new app version. However, one week after the start of the A/B test, only 15 percent of the treatment group has downloaded the update through the Google Play store.
- Doctors Without Borders is A/B testing a new message on their donation page. Visitors to the website are randomly split into two groups. The treatment group is assigned to the version of the site with the new message. However, only 60 percent of visitors go to the donation page where the new message is displayed.

# Compliance notation

In the basic potential outcomes framework the treatment assignment $$D_i$$ is randomly assigned to each subject. We implicitly assumed that the assignment and actual treatments are identical. Now we relax that assumption, so we must enrich our mathematical framework.

Let $$Z_i$$ indicate the assignment of subject $$i$$ as intended by the experiment designer. When treatment is assigned we have $$Z_i=1$$. When control is assigned we have $$Z_i=0$$. 

Let $$D_i \in {0, 1}$$ be the actual treatment received. However, because $$D_i$$ can differ from the assignment, we now have _potential outcomes for the treatment_: $$(D_i^0, D_i^1)$$! $$D^1$$ is the actual treatment when the subject is assigned to the treatment group. $$D^0$$ is the actual treatment when the control group is assigned. 

# Subject types

There are four possible combinations of assignment and actual treatment, and they each have a name:

1. _Compliers_: These subjects always do as they are assigned, so $$D_i = Z_i$$.
2. _Never-Takers_: These In a clinical example, this might be comparing a control group (no drug) to a treatment group (experimental drug)subjects never take the treatment, so $$D^0_i=0$$ and $$D^1_i = 0$$.
3. _Always-Takers_: These subjects always take the treatment, so $$D^0_i=1$$ and $$D^1_i = 1$$.
4. _Defiers_: These subjects do the opposite of the assignment, so $$D^0_i=1$$ and $$D^1_i = 0$$.

*Compliers* and *never-takers* are the most relevant types when we consider experimentation in a software context. The typical problem is that some users of a product will be assigned to receive the treatment but not receive it. Often this happens because they do not use the feature of the product on which the experimental treatment is operative. In other words, online experimentation is typically afflicted by one-sided noncompliance with never-takers. The problem of always-takers and defiers is unlikely because it is difficult or impossible for a user to gain access to the experimental feature.

# Estimating treatment effects when there is one-sided non-compliance

## Two potentially, biased approaches

### Naive "as-treated" analysis

The as-treated approach compares groups in terms of their actual treatment $$D_i$$, just as we have done throughout the class. We ignore non-compliance in the analysis. However, when there is non-compliance, our treatment and control groups are no longer correctly randomized. Each person's actual treatment $D_i$ may have an unknown correlation with other variables, including their potential outcomes. Non-compliance can invalidate the independence assumption $$(Y^0, Y^1) \perp D$$ because $D$ is no longer a simple, independent coin flip.

### Naive "per-protocol" analysis

The per-protocol analysis throws away the data of the non-compliers in a naive way. In the experiment on door-to-door political canvasing, a per-protocol analysis would compare the entire set of control voters to the subset of treatment households that were at home and actually received a face-to-face message. The primary shortcoming of this approach is that the randomization may no longer be reliable, which introduces bias. We should immediately be suspicious of throwing away the non-compliers in the treatment group because we not throwing them out of the control group. We do not know who the non-compliers are in the control group because no canvases went to the control households. We should suspect that complier type is related to the potential voting outcomes. Compliers are likely to spend more time at home on average and be more open to opening to door to listen to a stranger. In our mathematical notation this means that 

$$
E[Y^t \vert D=1,Z=1] \ne E[Y^t \vert D=0,Z=1].
$$
{: style="color:gray; font-size: 120%; text-align: center;"}

Another shortcoming is that the sample used in the analysis is a selected sample that may no longer be representative of the original sample. In other words, the ATE within the selected subsample may not be the same as the ATE in the overall sample.

## Two recommended approaches

The next two approaches are recommended for analyzing experiments with noncompliance. In any given experiment one or both maybe be applied. These approaches make clear that the cost of noncompliance is that the ATE cannot be identified. Instead each of the two methods identifies a different estimand, either the ITT effect or the CACE. The ITT can be viewed as the effect of the treatment _as a policy_, taking into account that some subjects may never receive the treatment. The CACE can be viewed as the direct, behavioral effect. Neither one can tell us the effect of the treatment on non-compliers, which is ultimately why the ATE cannot be identified.

### Itention-to-treat (ITT) analysis

In an intention-to-treat (ITT) analysis, we consider the assignment ($$Z_i$$) itself to be the treatment. We ignore $$D_i$$. The rest of the analysis is conducted substituting $$Z_i$$ for $$D_i$$ everywhere. In the canvasing experiment, we would calculate the turnout rate among the control voters and compare it to the turnout rate among the treatment voters, regardless of whether they actually received the face-to-face message. 

The caveat of the ITT approach is that we are not estimating the ATE of the treatment itself. The ITT is a different estimand that can be thought of as the average treatment effect of _being assigned to the treatment group_. The ATE is the average treatment effect of _actually receiving the treatment_. We can write the ITT as 

$$
ITT = E[Y_i(Z_i=1) - Y_i(Z_i=0)],
$$
{: style="color:gray; font-size: 120%; text-align: center;"}

noting that implicit in this formulation is that each individual $$i$$ is either a complier or a never-taker, and we have suppressed the notation making that distinction.

Our usual identification conditions apply. In particular, we need the independence assumption with respect to $$Z$$ and the potential outcomes. In that case we can compare the average outcome $$Y$$ between the group *assigned* to treatment and the group *assigned* to control. 

### Complier average causal effect (CACE)

The last approach is to estimate the complier average causal effect, which is also known as the local average treatment effect (LATE). The LATE is the average treatment effect, when the population of interest is restricted just to _compliers_. In causal inference notation this is $$E[Y_i^1 - Y_i^0 \vert D_i^1=1]$$. We could also write it as $$ATE(D(1) = 1)$$, where the treatment $$D$$ is written as a function of the assignment $$Z$$. We will consider three methods of estimating the CACE.

#### Adjusting the ITT

Under standard assumptions in the one-sided noncompliance case we can estiamte the CACE by estimating the ITT and the proportion of compliers,

$$ 
CACE = \tfrac{ITT}{P(D^1 = 1)} = \tfrac{ITT}{\text{proportion of compliers}}.
$$
{: style="color:gray; font-size: 120%; text-align: center;"}

To estimate the proportion of compliers, we need to have data on which subjects in the treatment group actually received the treatment.

An important challenge of estimating the ATE in this way is that statistical power can be greatly reduced. The standard error of the estimated CACE is approximately equal to the standard error of the ITT but inflated by $$\tfrac{1}{\text{proportion of compliers}}$$. Therefore, if the compliance rate is only 20 percent, then our standard error will be inflated by 5x! To achieve the same level of statistical power, we may need to increase our sample size by 25 times. Intuitively this is because if only 20 percent of our subjects are compliers, then only 20 percent of our sample is actually useful for estimating the CACE.


#### Directly restricting the sample to compliers

Since the CACE is simply the ATE by restricted to the subsample of compliers, we may be able to estimate it directly. To that we need know which subjects are compliers, in both the treatment group and the control group. This means we need to have data on which treatment subjects were actually treated and which control subjects _would have been treated if they were in the treatment group_. Obtaining that data often requires extra measures. In the canvasing example, we would need to send canvasers even to the control group to check if the voter is home and willing to open the door. We might do this using a "placebo" treatment, for example, delivering a face-to-face message about a non-political topic that should not have any effect on votign behavior. In an example of an A/B test on a specific web page, we need to have logging of all users' visits to that page. If we have this kind of data, we can throw away all the noncompliers and analyze only the compliers.

For a given sample size, directly estimating the CACE delivers increases precision. By incorporating additional information about who is a complier, we can eliminate the uncertainty resulting from estimating the proportion of compliers and the "noises" resulting  from noncompliers in both the treatment and control groups. 

