---
title: Prompt Engineering
description: Notes on OpenAI Lilian Weng article on Prompt Engineering
date: March 23, 2023
categories: [ai, links]
layout: post
toc: false
comments: true
hide: false
search_exclude: false
---

- Prompt Engineering, also known as In-Context Prompting, refers to methods for how to communicate with LLM to steer its behavior for desired outcomes without updating the model weights
  - https://lilianweng.github.io/posts/2021-01-02-controllable-text-generation/
- Few-shot Learning
  - Many studies looked into how to construct in-context examples to maximize the performance and observed that choice of prompt format, training examples, and the order of the examples can lead to dramatically different performance, from near random guess to near SoTA.
  - Biases
    - Recency bias: last label influences too much
    - Common token bias: repeted token have higher chance of appearing in the response
    - Majority label bias: if examples are not balanced, may influence the outcome
  - Tips for Example Selection: Choose examples that are semantically similar to the test example using -NN clustering in the embedding space
  - Tips for Example Ordering: Keep the selection of examples diverse, relevant to the test sample and in random order to avoid majority label bias and recency bias.
- Instruction Prompting
  - When interacting with instruction models, we should describe the task requirement in details, trying to be specific and precise and avoiding say “not do something” but rather specify what to do.

source: https://lilianweng.github.io/posts/2023-03-15-prompt-engineering/
