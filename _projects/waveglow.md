---
title: "WaveGlow: a Flow-based Generative Network for Speech Synthesis"
collection: projects
author: Ryan Prenger, Rafael Valle and Bryan Catanzaro
permalink: /WaveGlow
excerpt: 'WaveGlow is an invertible neural network that can generate high quality speech efficiently from mel-spectrograms.'
date: 2018-10-29
---

<p align="center" markdown="1">
![WaveGlow](images/waveglow_logo.png){:width="70%"}
</p>

## Ryan Prenger, Rafael Valle, and Bryan Catanzaro

In our recent [paper], we propose WaveGlow: a flow-based network capable
of generating high quality speech from mel-spectrograms. WaveGlow
combines insights from [Glow] and [WaveNet] in order to provide fast,
efficient and high-quality audio synthesis, without the need for
auto-regression. WaveGlow is implemented using only a single network,
trained using only a single cost function: maximizing the likelihood of
the training data, which makes the training procedure simple and
stable.

Our [PyTorch] implementation produces audio samples at a rate of more than
500 kHz on an NVIDIA V100 GPU and Mean Opinion Scores show that it delivers
audio quality as good as the best publicly available WaveNet
implementation. 

Below we provide real samples and synthesized samples using our WaveGlow model, Griffin-Lim 
and an open source [WaveNet implementation].

[paper]: https://arxiv.org/abs/1811.00002
[WaveNet implementation]: https://github.com/r9y9/wavenet_vocoder
[Glow]: https://blog.openai.com/glow/
[WaveNet]: https://deepmind.com/blog/wavenet-generative-model-raw-audio/
[PyTorch]: http://pytorch.org

{::nomarkdown}
{% include_relative waveglow_files.html %}
{:/}