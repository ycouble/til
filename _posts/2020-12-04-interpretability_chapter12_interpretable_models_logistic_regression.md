---
title: "Interpretability (Part 3)"
description: "Notes on Interpretable Machine Learning by C. Molnar"
layout: post
toc: false
comments: true
hide: false
search_exclude: false
categories: [xai, en, ml]
---

## Some quotations
A change in a feature by one unit changes the odds ratio (multiplicative) by a factor of exp(βj). We could also interpret it this way: A change in xj by one unit increases the log odds ratio by the value of the corresponding weight. 
    
These are the interpretations for the logistic regression model with different feature types:
* Numerical feature: If you increase the value of feature xj by one unit, the estimated odds change by a factor of exp(βj)
* Binary categorical feature: One of the two values of the feature is the reference category (in some languages, the one encoded in 0). Changing the feature xj from the reference category to the other category changes the estimated odds by a factor of exp(βj).
* Categorical feature with more than two categories: One solution to deal with multiple categories is one-hot-encoding, meaning that each category has its own column. You only need L-1 columns for a categorical feature with L categories, otherwise it is over-parameterized. The L-th category is then the reference category. You can use any other encoding that can be used in linear regression. The interpretation for each category then is equivalent to the interpretation of binary features.
* Intercept β0: When all numerical features are zero and the categorical features are at the reference category, the estimated odds are exp(β0). The interpretation of the intercept weight is usually not relevant.

Another disadvantage of the logistic regression model is that the interpretation is more difficult because the interpretation of the weights is multiplicative and not additive.

On the good side, the logistic regression model is not only a classification model, but also gives you probabilities. This is a big advantage over models that can only provide the final classification. Knowing that an instance has a 99% probability for a class compared to 51% makes a big difference.

