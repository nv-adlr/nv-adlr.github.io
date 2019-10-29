---
title: "Mellotron: Multispeaker expressive voice synthesis by conditioning on rhythm, pitch and global style tokens"
collection: projects
author: Rafael Valle, Jason Li, Ryan Prenger, and Bryan Catanzaro
permalink: /Mellotron
excerpt: 'Mellotron is a multispeaker voice synthesis model that can make a voice emote and sing without emotive or singing training data'
date: 2019-10-23
---

<p align="center" markdown="1">
![Mellotron](images/mellotron_logo.png){:width="70%"}
</p>


## *Rafael Valle, Jason Li*, Ryan Prenger, and Bryan Catanzaro

In our recent [paper], we propose **Mellotron**: a multispeaker voice synthesis model based on [Tacotron 2 GST] that can make a voice emote and sing without emotive or singing training data. By explicitly conditioning on rhythm and continuous pitch contours from an audio signal or music score, Mellotron is able to generate speech in a variety of styles ranging from read speech to expressive speech, from slow drawls to rap and from monotonous voice to singing voice.

Unlike other methods, we train Mellotron using only read speech data without alignments between text and audio. We evaluate our models using the LJSpeech and LibriTTS datasets. We provide F0 Frame Errors and synthesized samples that include style transfer from other speakers, singers and styles not seen during training, procedural manipulation of rhythm and pitch and choir synthesis.

Below we provide style transfer and singing voice synthesis samples produced with Mellotron and [WaveGlow].
Code for training and inference, along with a pretrained model on LJS and LibriTTS, will available on our [Github repository].

[paper]: https://arxiv.org/abs/1910.11997
[Tacotron 2 GST]: https://arxiv.org/abs/1803.09017
[Github repository]: http://github.com/NVIDIA/mellotron
[WaveGlow]: https://github.com/NVIDIA/waveglow

{::nomarkdown}
{% include_relative mellotron_files.html %}
{:/}
