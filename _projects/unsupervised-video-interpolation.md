---
title: "Unsupervised Video Interpolation Using Cycle Consistency"
collection: publications
permalink: /publication/2019-UnsupervisedVideoInterpolation
excerpt: 'We propose unsupervised techniques to synthesize high frame rate videos directly from low frame rate videos using cycle consistency. We also introduce a pseudo-supervised loss term that enforces the interpolated frames to be consistent with predictions of a pre-trained interpolation model. The pseudo-supervised loss term, used together with cycle consistency, can effectively adapt a pre-trained model to a new target domain. We show results that significantly reduce the domain gap problem in video frame interpolation.'
date: 2019-09-26
venue: 'International Conference on Computer Vision (ICCV)'
paperurl: 'https://arxiv.org/abs/1906.05928'
citation: 'Fitsum A. Reda, Deqing Sun, Aysegul Dundar, Mohammad Shoeybi, Guilin Liu, Kevin J. Shih, Andrew Tao, Jan Kautz, Bryan Catanzaro, "Unsupervised Video Interpolation Using Cycle Consistency". In ICCV 2019.'
---
Learning to synthesize high frame rate videos via interpolation requires large quantities of high frame rate training videos, which, however, are scarce, especially at high resolutions. Here, we propose unsupervised techniques to synthesize high frame rate videos directly from low frame rate videos using cycle consistency. For a triplet of consecutive frames, we optimize models to minimize the discrepancy between the center frame and its cycle reconstruction, obtained by interpolating back from interpolated intermediate frames. This simple unsupervised constraint alone achieves results comparable with supervision using the ground truth intermediate frames. We further introduce a pseudo supervised loss term that enforces the interpolated frames to be consistent with predictions of a pre-trained interpolation model. The pseudo supervised loss term, used together with cycle consistency, can effectively adapt a pre-trained model to a new target domain. With no additional data and in a completely unsupervised fashion, our techniques significantly improve pre-trained models on new target domains, increasing PSNR values from 32.84dB to 33.05dB on the Slowflow and from 31.82dB to 32.53dB on the Sintel evaluation datasets.

# Videos : Results and Comparisions

Upscaling framerate by 4x or 8x using our unsupervised techniques. We use the [Super SloMo](https://arxiv.org/abs/1712.00080) multi-frame interpolation model as our base network. Our techniques are, however, general and can be applied on any video interpolation model, for instance [DVF](https://arxiv.org/abs/1702.02463) as described in our paper.
RGB flag indicate synthesized frame. 

## Fully Unsupervised Training for 4x Framerate Upscaling
Synthesis of three intermediate frames for every pair of input frames on an example [YouTube-8M](https://research.google.com/youtube8m/) video. [Download](https://drive.google.com/open?id=1DgF-0r1agSy9Y77Bthm_w135qOABc3Xd).
<video poster="https://drive.google.com/uc?export=view&id=1YN-nKJW4i23gC1ueZFtA5GEN_p7CAWGA" width="960" height="540" controls>
  <source src="https://drive.google.com/uc?export=view&id=1DgF-0r1agSy9Y77Bthm_w135qOABc3Xd" type="video/mp4">Your browser does not support the video tag.
</video>

## Unsupervised Fine-tuning for 8x Framerate Upscaling
Comparision of a Supervised Pre-training (baseline) with Unsupervised Fine-tuning (proposed) on Super SloMo for an example [Slowflow](http://www.cvlibs.net/projects/slow_flow/) video. [Download](https://drive.google.com/open?id=1D_dSv8JB0-GHKFkN2YmCIPehoyt7ca4A).
<video poster="https://drive.google.com/uc?export=view&id=1-JjK8w4mjKS3uHn9YwTRRuPXowq6fyU5" width="960" height="270" controls>
  <source src="https://drive.google.com/uc?export=view&id=1D_dSv8JB0-GHKFkN2YmCIPehoyt7ca4A" type="video/mp4">Your browser does not support the video tag.
</video>

Comparision of a Supervised Pre-training (baseline) with Unsupervised Fine-tuning (proposed) on Super SloMo for an example [Slowflow](http://www.cvlibs.net/projects/slow_flow/) video. [Download](https://drive.google.com/open?id=1DeD3oZdT_c3GGz8BOGYpF4rrLWLlB9GU).
<video poster="https://drive.google.com/uc?export=view&id=1JJ2DizaLbPBZiGwFoG9aPoEx-RsmneWZ" width="900" height="360" controls>
  <source src="https://drive.google.com/uc?export=view&id=1DeD3oZdT_c3GGz8BOGYpF4rrLWLlB9GU" type="video/mp4">Your browser does not support the video tag.
</video>