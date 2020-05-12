---
title: "Flowtron: an Autoregressive Flow-based Generative Network for Text-to-Speech Synthesis"
collection: projects
author: Rafael Valle, Kevin Shih, Ryan Prenger, and Bryan Catanzaro
permalink: /Flowtron
excerpt: 'Flowtron is an autoregressive flow-based generative network for text-to-speech synthesis with direct control over speech variation and style transfer'
date: 2020-04-30
---

<p align="center" markdown="1">
![Flowtron](images/flowtron_logo.png){:width="70%"}
</p>


## Rafael Valle, Kevin Shih, Ryan Prenger, and Bryan Catanzaro

In our recent [paper] \([pdf]\) we propose **Flowtron**: an autoregressive flow-based generative network for text-to-speech synthesis with direct control over speech variation and style transfer. Flowtron combines insights from [IAF] and optimizes [Tacotron 2] in order to provide high-quality and controllable mel-spectrogram synthesis. 

FlowTron is optimized by maximizing the likelihood of the training data, which makes training simple and stable. Flowtron learns an invertible mapping of data to a latent space that can be manipulated to control many aspects of mel-spectrogram synthesis.

Below we provide samples produced with Flowtron and [WaveGlow]. Code for training and inference, along with pretrained models on LJS and LibriTTS, will be available on our [Github repository].

[paper]: https://arxiv.org
[pdf]: http://docs.google.com/uc?export=open&id=13vDXjdBWz3uP4IrM2PMwym4YCW60AKNh
[Tacotron 2]: https://arxiv.org/abs/1712.05884
[IAF]: https://arxiv.org/abs/1606.04934
[Github repository]: http://github.com/NVIDIA/flowtron
[WaveGlow]: https://github.com/NVIDIA/waveglow

{::nomarkdown}
{% include_relative flowtron_files.html %}
{:/}