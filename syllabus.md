---
title: Syllabus
author: Kyle Carlson
layout: default
---

Instructor: Kyle Carlson

Experiments are commonly used in software and internet businesses to evaluate product changes, marketing strategies, and other decisions. Expertise in applying experiments in this context is valuable skill that this course will help students develop. We will cover fundamentals of causality, experimental design, and statistical analysis. In addition we will devote special attention to the particular challenges of experiments in a software business environment. We will analyze how these challenges relate to the theory of causality and assumptions of statistical approaches. Students will learn to build intuition about these problems using simulation.

## Course Goals
Students should learn the following by completing the course:
- How to think critically about causality and experiments. This skill includes a critical and skeptical attitude towards claims and analysis regarding causality. Students should understand the fundamental problem of causal inference and to what extent experiments can solve the problem when implemented correctly. Students should be able to understand current debates about the reliability of experiments and statistics.
- How to use experiments to understand causal relationships and make decisions. This skill includes knowledge of experiment design and statistical analysis. However, because this is an applied course, students should learn how to adapt the design and analysis towards the practical goals of their organization.
- How to identify and address practical complications in experiments. Experiments in software or internet businesses present numerous challenges that are less often seen in a research setting. Students should learn how to minimize practical threats to the validity of their experiments.
- How to plan and evaluate experiments using simulations. Experimenters can use simulations to quickly understand complications and potential solutions. This skill is especially useful in the applied setting when theoretical approaches may be too time consuming or difficult to communicate.

## Lecture plan
### Class schedule
The course roughly splits in half. The first half focuses on establishing frameworks for thinking about experiments rigorously. In the second half we apply those frameworks to understand the complications of experimentation in practice.

| Date | Topics | 
| 8/19 *Monday* | Lecture 1 - Causality fundamentals
| ~~8/26~~ (Canceled) |
| 8/30 *Friday* | Lecture 2 - Uncertainty
| ~~9/2~~ (Canceled: Labor Day)  |
| 9/9 *Monday* | Lecture 3 - Sampling complications |
| 9/16 *Monday* | Lecture 4 - Estimation |
| 9/23 *Monday* | Lecture 5 - Complications in practice |
| 9/30 *Monday* | Lecture 6 - Decision-making |
| 10/7 *Monday* | Lecture 7 - Large-scale experimentation |
| 10/14 *Monday* | Lecture 8 - TBD |


### Class outlines
#### Lecture 1 - Causality fundamentals
- Example cases: Why is experimentation important? Why is it challenging?
- Overview of the course
- Topics: Potential outcomes model, treatment effects, identification, selection bias, independence assumption, SUTVA
- In-class exercises: Law of large numbers, selection bias, conditional independence

#### Lecture 2 - Uncertainty
- Review material from lecture 1 + commentary on problems
- Topics
  - Identification vs. statistical inference
  - "Repeated sampling" 
  - Standard errors and confidence intervals
  - Data-generating processes and models
  - Hypothesis testing and p-values
  - Power
  - Commentary about other forms of uncertainty (randomization, Bayesian)
 - In-class exercises
   - Central limit theorem
   - Confidence intervals
   - Hypothesis testing

#### Lecture 3 - Sampling complications
 - Review lecture 2 concepts + commentary on problems
 - Topics
   - Precision of estimates and power
   - Randomization types: 
     - Simple randomization (independent coin flips)
     - Block randomization (balance sample sizes)
     - Stratified randomization (block randomization on covariates)
     - Paired design (2-unit blocks)
     - Clustering
     - Factorial (mention but cover later)
  - In-class exercises
    - Simulate power for simple randomization vs. block randomization
    - Simulate simple vs. paired
    - Simulate simple randomization vs. stratified randomization
    - Simulate simple vs clustering

#### Lecture 4 - Estimation
  - Review causality fundamentals + uncertainty + commentary on problems
  - Topics
    - Calculating means
    - Regression for calculating means
    - Linear probability model
	- Matching to achieve conditional independence
	- Regression to achieve conditional independence
    - Standard errors for mean and regressions
    - Hypothesis testing with regression
    - Covariates to improve precision and appropriate use in experiments
    - Robust and clustered standard erorrs
  - In-class exercises
    - Calculating means and probabilities with regression (identifying various distributions)
    - Achieving conditional independence with matching and regression
    - Sampling distribution of point estimates vs. standard error estimates
    - Covariates in regression

#### Lecture 5 - Complication in practice and the culture of experiments
 - Review bias, power, and estimation. We now have a theoretical foundation to rigorously think about the complications of experimentation in practice.
 - Topics
   - Planning experiments: Power, unknown sample sizes, cyclicality
   - Instrumentation, errors, data loss
   - Roll-out: Bias and conditional independence
   - Compliance: Intention-to-treat, "trigger analysis"
   - Early-stopping, "peeking"
   - Bias from early-stopping due to errors
   - Clustering and users of a site/software (cookies, sessions, pages)
   - Unintended treatment-control differences (e.g., error rates, load time)
   - Experiments that affect sample size
 - In-class exercises
   - Simulate bias from weekly cyclicality + roll-out + conditional independence
   - Simulate non-compliance and ITT
   - Simulate peeking
   - Simulating bias from clustering (application of clustered SEs a la Athey)

#### Lecture 6 - Experiments and economic decision-making
- Review hypothesis testing and power and reasons for experimentation
- Topics
  - Differences between research experiments and applied experiments
  - Hypothesis tests and decision-making: Paradox if simple null hypothesis
  - Value of information
  - Deciding whether to do an experiment
  - Deciding which experiment to do
  - Experimentation for hypothesis-testing vs. optimization
- In-class exercises
  - Simulate researcher vs. applied and hypothesis testing errors
  - Simulate hypothesis testing vs. utility in an applied context
  - Simulate value of information of different experiment designs
  - Simulating multiple treatments and optimization

#### Lecture 7 - Large-scale experimentation
- Review decision-making
- Topics
  - Large numbers of outcomes. Multiple hypothesis testing
  - Multiple treatments and interacting experiments
  - Allocating samples across experiments
  - "Experiment platforms"
  - Reporting experiment results
  - Small effects multiplied across huge scales
- In-class exercises
  - Simulating many outcomes and multiple hypothesis testing
  - Simulating multiple concurrent experiments
  - Simulating optimal allocations across experiments
 
#### Lecture 8 - TBD


## Related courses

- ["The Statistics of Causal Inference in the Social Sciences" (Sekhon, UC Berkeley)](http://sekhon.berkeley.edu/causalinf/)
- ["Measuring Impact in Practice" (Broockman, Stanford GSB)](https://explorecourses.stanford.edu/search?view=catalog&filter-coursestatus-Active=on&q=MGTECON%20383:%20Measuring%20Impact%20in%20Practice&academicYear=20162017) [[materials]](https://www.dropbox.com/s/dyyto6qmme4edu4/Shared%20Publicly%20-%20Broockman%20Measuring%20Impact%20Class-20180605T040309Z-001.zip?dl=0)
- ["From Data to Decisions: The Role of Experiments" (Luca, Harvard Business School)](https://webcache.googleusercontent.com/search?q=cache:hta0-Wdar8gJ:https://www.hbs.edu/coursecatalog/2205.html+&cd=2&hl=en&ct=clnk&gl=us)
- ["Empirical Microeconomics" (Jakiela and Ozier, U of MD)](http://economics.ozier.com/econ626/)
- ["Statistical Learning and Causal Inference for Economics
" (DiTraglia, UPenn)](https://ditraglia.com/econ224/)


## Reference books

- _Mostly Harmless Econometrics: An Empiricist's Companion_ by Joshua D. Angrist and Jörn-Steffen Pischke 
- _Counterfactuals and Causal Inference: Methods and Principles for Social Research_ 
 by Stephen L. Morgan and Christopher Winship
 - _Econometric Analysis of Cross Section and Panel Data_ by Jeffrey M. Wooldridge


## Readings

- ["Let's Take the Con Out of Econometrics." Edward E. Leamer. _American Economic Review_ (1983)](http://faculty.smu.edu/millimet/classes/eco7321/papers/leamer.pdf?mod=article_inline)
- ["The Credibility Revolution in Empirical Economics: How Better Research Design Is Taking the Con out of Econometrics." Joshua D. Angrist and Jörn-Steffen Pischke. _Journal of Economic Perspectives_ (2010)](https://www.aeaweb.org/articles?id=10.1257/jep.24.2.3)
- [EconTalk: "Susan Athey on Machine Learning, Big Data, and Causation.
Sept. 12 2016"](http://www.econtalk.org/susan-athey-on-machine-learning-big-data-and-causation/#audio-highlights): Note that the discussion covers the Ed Leamer article above and the work of Card and Krueger (see slides).
- [How to Design (and Analyze) a Business Experiment (Hauser and Luca)](https://hbr.org/2015/10/how-to-design-and-analyze-a-business-experiment)
- ["Consumer heterogeneity and paid search effectiveness: A large‐scale field experiment." Blake, Thomas, Chris Nosko, and Steven Tadelis. Econometrica (2015)](http://funginstitute.berkeley.edu/wp-content/uploads/2013/12/Tadelis.pdf)
- ["Online Experimentation at Microsoft"](https://ai.stanford.edu/~ronnyk/ExPThinkWeek2009Public.pdf)
- Challenging Problems in Online Controlled Experiments: <a href="http://ide.mit.edu/sites/default/files/CODE%202015%20Program.pdf" target="_blank" rel="noopener noreferrer">MIT Code</a> 2015 invited talk (10/17/2015): Discussion of esoteric SUTVA violations
- Trustworthy Online Controlled Experiments at Large Scale <a href="http://bit.ly/decisionsConfRonnyk">slides</a>