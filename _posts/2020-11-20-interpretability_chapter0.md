---
title: "Interpretability (Part 1)"
description: "Notes on Interpretable Machine Learning by C. Molnar"
layout: post
toc: false
comments: true
hide: false
search_exclude: false
categories: [xai, en, ml]
---

# Interpretable Machine Learning
Some notes on the small book [Interpretable Machine Learning](https://christophm.github.io/interpretable-ml-book/) by Christoph Molnar.

## Introduction and overview
Christoph Molnar overviews in his book the concept of model interpretability, and surveys several ways to help ML engineers design more interpretable machine learning model, i.e. enable humans to understand the reasons why the ML model outputs what it outputs instead of any the other possible answers.
The motivation for model interpretability is qui immediate, as a matter of fact it is often observed that results from a recommender system such as YouTube video recommendations are somewhat obsucre, and the more importance machine learning algorithms take into our lives, the more important it will be that we are able to understand the "whys" of the ML models decisions.

Efforts are being made by many of the major actors, as for example Amazon's recommendation systems providing a set of articles that are commonly bought together, or the "customers that bought what you just bought also often purchased this other article" and such. 
However, it is far from being widespread, and it also has an important impact for many many companies or labs that usually focus on ML model performance and trust almost blindly the output of their model. 
Note that this is not only applicable to ML, it is also often the case for sufficiently complicated models and algorithms such are optimization (among which discrete optimization is probably a great candidate), where we are unable to track or get an intuition of why the result is such and not something completely different.

The consequences of bad model interpretability are numerous, but I see one very important point, which is functional model debugging failures.
If you trust you model/algorithm blindly, it then becomes very hard to see if the model behaves as it should. 
For example, imagine that you have a discrete optimization problem, which is NP-hard and that you solve using a custom algorithm. 
Since the problem is too complex, it is nearly impossible (or too long) to know the optimal solution for a large instance, and you are left with trusting your algorithm or only confronting your algorithm to the known optimal on very small instances that may not be representative of larger instances, and certainly not a proof. 
In this kind of situations, it becomes very important to have some other ways of checking the result of your algorithm, its consistency, why the result on such instance is much lower than on other similar instances, etc.

The same applies to machine learning, with an even more critical factor which is that ML models are much more obscure on their internal mechanisms.
Indeed, unlike instruction-based algorithms which are easily probed and for which each step bears meaning, machine learning models internal steps bear no meaning and are only the result of the model training on data.

## Model interpretability
What the author defines as interpretability is the capacity of a model/solution to justify its output. 
First he mentions that there are models that are intrinsincly interpretables sur as linear regressions, decisions trees and such where the internal parameter values already offers a sufficiently good explanation of the output.
For example, for a linear regression, the weights of each feature will tell us how important are each feature compared to the others.

But for more sophisticated model, interpretability is obtained through more indirect or approximation methods, among which:

- Partial dependance plots (WIP)
- Individual Conditional Expectations (WIP)
- Accumulated Local Effects (WIP)
- Feature Interaction (WIP)
- Feature Importance (WIP)
- Global Surrogate, which consist of approximating the output of the model by a simple, intepretable machine learning model.
- Local Surrogate, which is the same as global but only for the neighborhood of an instance.
- Shapley Values, inspired from game-theory where the relative importance of each feature in the "coalition" is derived from similar instances in the dataset.

## Interpretability evaluation
Interpretability is not easily evaluated since there is no quantification of interpretability and since it depends largely on the social and technological context of the recipient of explanations.
Obviously the explaination of the model output will not be the same for an field expert and for the end customer of a recommender system.

Several criteria of evaluation are underline in the book, first as general criteria for an explanation method, then for individual explanation produced by such methods:

- Explanation power: how expressive the provided explanations are (do they use natural language, if-then-else type conditions...)
- Translucency: how related to the model internal is the explanation (for example an explanation consisting in the weights for a linear model is a very translucent explanation)
- Portability: how tied to a model is the explanation, i.e. can it be ported to other models without modifications (in the last example, the weight is not a portable explanation)
- Algorithmic complexity

Individual explanations:

- Accuracy: how fit to the data is the explanation
- Fidelity: how fit to the model output is the explanation
- Consistency: how robust to a model change is the explanation (closely related to the method portability)
- Stability (Always desirable): how robust to some small perturbations is the explanation
- Comprehensibility
- Certainty: how sure / confident in the explanation
- Degree of Importance: feature importance for explaining the data
- Novelty of the data: how exceptional is an instance compared to the known dataset
- Representativeness: range covered by the explanation

## Human friendly explanations
For a model to be easily intepretable by humans the author argues that the explanations should follow several rules:

- Explanations have to be selected: a good practice is to refrain from providing every possible bit of explainatory data and to limit the explanation to the 1-3 most important pieces of explanations.
- Explanations should be constrastive: usually humans prefer explanations that compare the instance with similar one that have a different ouput: "why is this instance so special that it doesn't output the same result as intuitively similar instances ?".
- Explanations should be targeted according to the target of the explanation
- Explanations should focus on abnormal data whenever possible: the most important piece of explanation is not always the most numerically significant but maybe the one which bears the highest abnormality.
- Explanations should be as truthful as possible, i.e. it should remain applicable on other instances and not be invalidated
- If no abnormality can be found, a general and probable explanation is also preferable to many pieces of unexceptional explanations.

