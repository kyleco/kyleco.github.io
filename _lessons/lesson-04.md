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
- _One-sided noncompliance_ means that only one of the groups in the experiment has noncompliance. Usually this means that some subjects in the treatment group fail to receive the treatment.


# Examples of noncompliance

- In a clinical study of a new heart disease drug, 50 percent of patients are randomly assigned to receive the new drug. However, some in the treatment group are nervous about potential side effects and secretly do not take the drug.
- In another study of anxiety treatment, the control group is assigned to take a standard anxiolytic drug, and the treatment group is assigned ketamine. However, some people in the control group take ketamine bought on the internet.
- Political scientists are studying the effect of door-to-door messages on voter turnout. A sample of registered voters is identified and 50 percent are randomly assigned to have a canvasser visit their house to deliver a face-to-face message about the political issues. Those in the control group are left alone. However, 20 percent of the people in the treatment group do not receive the message because they are not home when the canvasser visits their house.
- Researchers are studying child mortality in 50 rural villages in Indonesia. Half of the villages are randomly assigned to receive vitamin A supplements. However, a typhoon washes out the roads in one district which prevents 6 villages from receiving the supplement shipment.
- Delyverizely is A/B testing a new version of their Android mobile app. Half of the current users are randomly assigned to get the new app version. However, one week after the start of the A/B test, only 15 percent of the treatment group has downloaded the update through the Google Play store.
- Doctors Without Borders is A/B testing a new message on their donation page. Visitors to the website are randomly split into two groups. The treatment group is assigned to the version of the site with the new message. However, only 60 percent of visitors go to the donation page where the new message is displayed.

# Compliance notation

In the basic potential outcomes framework the treatment assignment $D_i$ is randomly assigned to each subject. We implicitly assumed that the assignment and actual treatments are identical. Now we relax that assumption, so we must enrich our mathematical framework.

Let $Z_i$ indicate the assignment of subject $i$ as intended by the experiment designer. When treatment is assigned we have $Z_i=1$. When control is assigned we ahve $Z_i=0$. 

Let $D_i in {0, 1}$ be the actual treatment received. However, because $D_i$ can differ from the assignment, we now have _potential outcomes for the treatment_! $D^1$ is the actual treatment when the subject is assigned to the treatment group. $D^0$ is the actual treatment when the control group is assigned. There are four possible combinations of assignment, and they each have a name:

1. _Compliers_: These subjects always do as they are assigned, so $D_i = Z_i$.
2. _Never-Takers_: These subjects never take the treatment, so $$D^0_i=0$$ and $$D^1_i = 0$$.
3. _Always-Takers_: These subjects always take the treatment, so $$D^0_i=1$$ and $$D^1_i = 1$$.
4. _Defiers_: These subjects do the opposite of the assignment, so $$D^0_i=1$$ and $$D^1_i = 0$$.

Compliers and never-takers are the most relevant types when we consider experimentation by internet companies. The typical problem is that some users of a product will be assigned to receive the treatment but not receive it. Often this happens because they do not use the feature of the product on which the experimental treatment is operative. In other words, online experimentation is typically afflicted by one-sided noncompliance with never-takers. The problem of always-takers and defiers is unlikely because it is difficult or impossible for a user to gain access to the experimental feature.