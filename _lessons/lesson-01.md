---
title: Lesson 1 - Causality Fundamentals
author: Kyle Carlson
layout: default
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

The nature of causality is the subject of debate by statisticians, economists, computer scientistis, and philosophers. We are in an economics course, so we will follow the conceptual framework most popular with economists, the potential outcomes model, also known as, the (Neyman-)Rubin causal model. This framework underlies thousands of observational studies and experiments in economics, political science, and sociology.

## Potential outcomes model

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

