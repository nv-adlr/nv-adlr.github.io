---
title: "Large Scale Language Modeling: Converging on 40GB of Text in Four Hours"
collection: publications
permalink: /publication/2018-large-batch-lm
excerpt: 'This paper shows how to do exa-scale distributed, large batch, mixed precision training of language models with investigations into the successes and limitations of large batch training on publicly available language datasets.'
date: 2018-08-03
venue: '2018 High Performance Machine Learning Workshop'
paperurl: 'https://arxiv.org/abs/1808.01371'
citation: 'Raul Puri, Robert Kirby, Nikolai Yakovenko, Bryan Catanzaro, Large Scale Language Modeling: Converging on 40GB of Text in Four Hours. arXiv. 2018.'
---
This paper shows how to do distributed, large batch, mixed precision training of language models with investigations into the successes and limitations of large batch training on publicly available language datasets. We train a 4096-dimension multiplicative LSTM on amazon reviews: an expensive 20-exaflop per epoch process. Training is performed in mixed precision with tensorcores on 128 GPUs across 16 DGX-1V nodes. This work enables training of new large language models in hours instead of weeks or days. See our [GitHub](https://github.com/NVIDIA/sentiment-discovery).

