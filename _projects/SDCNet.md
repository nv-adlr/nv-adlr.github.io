---
title: "SDCNet: Video Prediction Using Spatially Displaced Convolution"
collection: publications
permalink: /publication/2018-SDCNet
excerpt: 'SDCNet is a 3D convolutional neural network proposed for frame prediction. The model takes as input a sequence of past frames and their inter-frame optical flows and generates a per-pixel kernel and motion vector. A future frame is then synthesised by sampling past frames guided by the motion vectors and weighted by the learned kernels. '
date: 2018-09-08
venue: 'European Conference on Computer Vision (ECCV)'
paperurl: 'https://arxiv.org/abs/1811.00684'
citation: 'Fitsum A. Reda, Guilin Liu, Kevin J. Shih, Robert Kirby, Jon Barker, David Tarjan, Andrew Tao, Bryan Catanzaro, SDCNet: Video Prediction Using Spatially Displaced Convolution. ECCV 2018.'
---
SDCNet is a 3D convolutional neural network proposed for frame prediction. The model takes as input a sequence of past frames and their inter-frame optical flows and generates a per-pixel kernel and motion vector. A future frame is then synthesised by sampling past frames guided by the motion vectors and weighted by the learned kernels. SDCNet is trainable on raw unlabelled video frames via self supervision, with little assumptions on the videos. We calculate the optical flows we input to our model using [flownet2-pytorch](https://github.com/NVIDIA/flownet2-pytorch).

