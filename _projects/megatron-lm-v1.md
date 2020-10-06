---
title: "MegatronLM’s Supercharged V1.0"
collection: projects
author: Mohammad Shoeybi, Raul Puri, Mostofa Patwary, Jared Casper, Patrick Legresley, Bryan Catanzaro
permalink: /MegatronLMV1
excerpt: 'We release version 1.0 of Megatron which makes the training of large NLP models even faster and sustains **62.4 teraFLOPs** in the end-to-end training that is **48%** of the theoretical peak FLOPS for a single GPU in a DGX2-H server.'
date: 2020-05-15
---

We recently released version 1.0 of Megatron-lm in our [github repository](https://github.com/NVIDIA/Megatron-LM). In addition to training support for the [world's largest BERT models](https://devblogs.nvidia.com/language-modeling-using-megatron-a100-gpu/) which established state-of-the-art results on the [RACE leaderboard](http://www.qizhexie.com/data/RACE_leaderboard.html), we performed several software optimizations to make the training of large NLP models even faster. As a result, our baseline model with 1.2 billion parameters now achieves **62.4 teraFLOPs** which is **48%** of the theoretical peak FLOPS for a single GPU in a DGX2-H server. This is a 60% improvement over our [previously](https://nv-adlr.github.io/MegatronLM) 39 teraFLOPs published number.

In addition, to test the effect of the optimizations on our model parallel scaling, we considered four GPT2 configurations ranging from 1.2B to 8.7B parameters with eight-way model parallelism. We fixed the batch size to 8 and increased the model parallel size as the model size increases. The scaling results are shown in Table 1. We observed excellent scaling numbers in both settings. For example, our experiments with 8.7 billion parameters and 8-way (8 GPUs) model parallelism achieved **79.6%** of linear scaling.

<table style="width:75%; text-align:center; margin-left:auto;margin-right:auto;font-size:15px;">
  <tr bgcolor="#15e7eb">
   <td style="width:15%"><strong>Number of Parameters (billions)</strong>
   </td>
   <td style="width:12%"><strong>Model Parallel GPUs</strong>
   </td>
   <td style="width:15%"><strong>Iteration Time (ms)</strong>
   </td>
   <td style="width:18%"><strong>Weak Scaling</strong>
   </td>
  </tr>
  <tr bgcolor="#21eb15">
   <td>1.2
   </td>
   <td>1
   </td>
   <td>1288
   </td>
   <td>Baseline (100%)
   </td>
  </tr>
  <tr bgcolor="#defa0f">
   <td>2.0
   </td>
   <td>2
   </td>
   <td>1242
   </td>
   <td>90.7%
   </td>
  </tr>
  <tr bgcolor="#21eb15">
   <td>4.2
   </td>
   <td>4
   </td>
   <td>1357
   </td>
   <td>86.5%
   </td>
  </tr>
  <tr bgcolor="#defa0f">
   <td>8.7
   </td>
   <td>8
   </td>
   <td>1508
   </td>
   <td>79.6%
   </td>
  </tr>
  <caption><strong>Table 1:</strong> Weak model parallel scaling.</caption>
</table>

We are constantly improving the computational efficiency of our code-base and will release the latest advancements in large scale LM training through our [github repository](https://github.com/NVIDIA/Megatron-LM).

