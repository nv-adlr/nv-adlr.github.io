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

Below are sample videos showing 2x frame rate upscaling using SDCNet. In each video, we interleave ground-truth and SDC predicted frames. Predicted frames are marked with a RGB flag near the top-right corner. <br />
To view the results frame by frames, we recommend to download and watch the videos in media players such as [VLC](https://www.videolan.org/vlc/index.html) or [SM](https://www.smplayer.info/). <br />
<b>Note</b>: If you are watching the videos directly as embedded in this webpage, the RGB flags may not be visible.

Various short clips (compared with [MC-Net](https://arxiv.org/pdf/1706.08033.pdf))
<video poster="https://drive.google.com/uc?export=view&id=1-sf93iMQ7RrBWEaxgwqwh17S9zNznbXS" width="960" height="540" controls>
  <source src="https://drive.google.com/uc?export=view&id=1-dkY6zM_08sM2wseoU60u4TORHBS96a8" type="video/mp4">Your browser does not support the video tag.
</video>

A sample video from [YouTube8M](https://research.google.com/youtube8m/)
<video poster="https://drive.google.com/uc?export=view&id=1-uHsdUUchRWeIgzoF34TvPTSomQxH_En" width="960" height="512" controls>
  <source src="https://drive.google.com/uc?export=view&id=1-hr957iLkvqtCWVNbTeztz4ibZHDZsoD" type="video/mp4">Your browser does not support the video tag.
</video>

A sample video from [YouTube8M](https://research.google.com/youtube8m/) video
<video poster="https://drive.google.com/uc?export=view&id=1-x2zbD3nns0d7pirXyCeFBxZvrBi1N_Z" width="960" height="512" controls>
  <source src="https://drive.google.com/uc?export=view&id=1-Y3RvIw2Pvu9z9Xp9B9LLmSchW6qK3tl" type="video/mp4">Your browser does not support the video tag.
</video>

A sample video from [CaltechPedestrian](http://www.vision.caltech.edu/Image_Datasets/CaltechPedestrians/)
<video width="640" height="480" controls>
  <source src="https://drive.google.com/uc?export=view&id=1-m8f6VV2GzLaFMhmGfRdbPw1jRgkeCVN" type="video/mp4">Your browser does not support the video tag.
</video>
