---
title: "Interpretability (Part 2)"
description: "Notes on Interpretable Machine Learning by C. Molnar"
layout: post
toc: false
comments: true
hide: false
search_exclude: false
categories: [xai, en, ml]
---

## Interpretable Models - Linear Regression
Linear regressions are simple models, which force the output to be a linear combination of the inputs. This means that the model is additive and thus easily explainable. The weights **are** the explaination.

Assumptions used to compute the confidence intervals:
- **Linearity**: forced by linear regression
- **Normality**: the probability of the target outcome given the feature should follow a normal distribution.
- **Homoscedasticity**: (constant variance) The variance of the error terms is assumed to be constant, which is generally not verified (variance typically often increase for large values)
- **Independence**: assumption that each instance in independent of any other, often not verified when you have several repeated measurements. If this is not the cas you need to have specific linear regression models such as mixed effect models of GEE (**//TODO**).
- **Fixed features**: inputs are considered exact and without measurement errors (always wrong, but it would be highly impractical otherwise)
- **Absence of multicollinearity**: When two features are strongly correlated, it blurrs the importance of the two (weights could go either way, and the model would be as efficient with only one of them)

## Interpretation
Interpretation depends on the type of feature:
- numerical: increase in feature --> increase * weight on outcome
- binary/categorical: presence/absence/selection --> weight on outcome
- intercept: if features are normalized and bin/cat 0 = reference --> outcome of all feature at their mean

### R-squared
Another important measurement is the R-squared, which tells how much of the total variance of the target outcome is explained by the model. 
- Higher R-squarred is better. 
- R² = 1 - square sum of errors / square sum of data variance.
- R² increases with the number of features, so it is better to use the adjusted R² = 1 - (1 - R²) (n-1)/(n-p-1) where p is the number of features and n the number of instances
- low adjusted R² --> not interpretable because it does not explain much of the variance.

### Feature importance
Importance of a feature in LR can be measured by the absolute value of its T-statistic, which is the estimated weight scaled with its standard error. (standard error = standard deviation of the outcome in a outcome = intercept + feature_weight * feature_value function)

--> possible to plot for each feature (like a facet plot) the `y = i + w*x` curve, with standard error shown, which highlights the distribution of the ground truths around the predictions.

## Visual interpretation
### Weight plots (https://christophm.github.io/interpretable-ml-book/limo.html#weight-plot)
Weight plots show for each feature the weight estimate and the standard error.
- Low SE represents a reliable feature
- High weight estimate means a high influence in the outcome
- Scaling makes the estimate weights more comparable

### Effect plots
Box plots for each feature. Only effects are represented. Effect = weight * value.
- vertical line = effect of the median
- box = 25% and 75% quantiles effect
- horizontal line = span +- 1.5 InnerQuartileRange
- dots = outliers

## Explain individual predictions
- Position the individual feature effects on an **Effect plot**: it enables to see how and why the outcome was decided (in particular, outlier effects are interesting)

## Encoding Categorical Features
Two encoding presented:
- Treatment coding: N - 1 features for N categories, 1 hot encoding
- Effect coding: Compare each category to the mean and use this value for encoding (only N-1 categories encoded)

## Do Linear models create good explanations ?
"linear models do not create the best explanations"
- **Contrastive**, but the reference instance is a data point where all numerical are 0 and categorical are at their reference category (usually meaningless).
If all features are mean centered, and cat features are effect coded, then the reference instance is the point where all features are at the mean.
- **Selectivity** can be achieved by LR using less features or training sparse linear models, but by default, explainations are not selective.
- **Truthfulness**: **yes** as long as the Linear Model is appropriate (aR² high).
- Linearity makes the explanation mode **general** and **simple**

## Sparse Linear Models
Regularization makes the model more frugal. 
- By adding a lambda * norm of the weights, in the minimization term, it penalizes models that have a lot of non null weights. The higher the lambda, the less feature are going into the model.
- Usually, lambda is tuned during cross-validation
- 
