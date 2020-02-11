---
title: "Image Inpainting for Irregular Holes Using Partial Convolutions"
collection: publications
permalink: /publication/partialconv-inpainting
excerpt: ''
date: 2018-12-09
venue: 'ECCV 2018'
paperurl: 'https://arxiv.org/abs/1804.07723'
citation: 'Guilin Liu, Fitsum A. Reda, Kevin J. Shih, Ting-Chun Wang, Andrew Tao, Bryan Catanzaro, Image Inpainting for Irregular Holes Using Partial Convolutions, Proceedings of the European Conference on Computer Vision (ECCV) 2018.'
---

Existing deep learning based image inpainting methods use a standard convolutional network over the corrupted image, using convolutional filter responses conditioned on both valid pixels as well as the substitute values in the masked holes (typically the mean value). This often leads to artifacts such as color discrepancy and blurriness. Post-processing is usually used to reduce such artifacts, but are expensive and may fail. We propose the use of partial convolutions, where the convolution is masked and renormalized to be conditioned on only valid pixels. We further include a mechanism to automatically generate an updated mask for the next layer as part of the forward pass. Our model outperforms other methods for irregular masks. We show qualitative and quantitative comparisons with other methods to validate our approach.


## Video

<iframe width="853" height="480" src="https://www.youtube.com/embed/gg0F5JjKmhA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Media Coverage (Selected)

[Fortune](http://fortune.com/2018/04/24/nvidia-artificial-intelligence-images/), [Forbes](https://www.forbes.com/sites/nvidia/2018/06/15/ai-research-is-pushing-the-limits-of-whats-possible/?linkId=100000002773533#54ef3a64ae75), [Fast Company](https://www.fastcompany.com/40563129/ai-can-now-reconstruct-your-exs-scratched-out-face-in-photos), [Engadget](https://www.engadget.com/2018/04/24/nvidia-ai-fixes-photos/), [SlashGear](https://www.slashgear.com/nvidia-neural-network-reconstructs-images-with-missing-parts-25528443/), [Digital Trends](https://www.digitaltrends.com/photography/nvidia-inpainting-ai-healing-brush-tool/), [TNW](https://thenextweb.com/artificial-intelligence/2018/04/24/nvidias-ai-reconstructs-partially-erased-images-with-jaw-dropping-accuracy/), [eTeknix](https://www.eteknix.com/nvidia-shows-off-impressive-ai-photo-reconstruction-abilities/), [Game Debate](http://www.game-debate.com/news/24974/nvidias-new-ai-tech-can-reconstruct-corrupt-images-with-near-perfect-results), [Alphr](http://www.alphr.com/artificial-intelligence/1009180/nvidia-ai-restore-damaged-old-photos), [Gizbot](https://www.gizbot.com/news/nvidia-s-new-ai-imaging-technique-can-resurrect-your-old-damaged-pictures-050009.html), [Fossbytes](https://fossbytes.com/nvidia-imaging-technique-reconstruct-photos/) [Techradar](https://www.techradar.com/news/nvidias-amazing-deep-learning-tool-can-reconstruct-incomplete-photos), [Beeborn](https://beebom.com/nvidia-feature-repair-images/), [Bit-tech](https://www.techradar.com/news/nvidias-amazing-deep-learning-tool-can-reconstruct-incomplete-photos), [Hexus](http://www.hexus.net/tech/news/software/117515-nvidia-shows-ai-tech-realistic-reconstruction-photos/), [HotHardWare](https://hothardware.com/news/nvidia-inpainting-ai-rebuild-corrupted-damaged-images), [BleepingComputer](https://www.bleepingcomputer.com/news/technology/nvidia-develops-ai-that-reconstructs-corrupted-images/),[hardocp](https://www.hardocp.com/news/2018/04/23/nvidia_ai_inpainting_cool_as_hell), [boingboing](https://boingboing.net/2018/04/23/a-i-reconstructs-incomplete-p.html), [PetaPixel](https://petapixel.com/2018/04/23/nvidias-ai-powered-content-aware-fill-is-mind-blowing/), [&#25628;&#29392;](http://www.sohu.com/a/229392616_473283), [&#26032;&#28010;](http://t.cj.sina.com.cn/articles/view/1649597805/6252dd6d019005h9n),[&#37327;&#23376;&#20301;(&#30693;&#20046;)](https://zhuanlan.zhihu.com/p/36116821)

## Online Demo
[https://www.nvidia.com/research/inpainting/](https://www.nvidia.com/research/inpainting/)

## Code
[https://github.com/NVIDIA/partialconv](https://github.com/NVIDIA/partialconv)

## Data (NVIDIA Irregular Mask Dataset)

### Training Set

To train the network, please use random augmentation tricks including random translation, rotation, dilation and cropping to augment the dataset. For our training, we use threshold 0.6 to binarize the masks first and then use from 9 to 49 pixels dilation to randomly dilate the holes, followed by random translation, rotation and cropping.

[NVIDIA Irregular Mask Dataset: Training Set](https://www.dropbox.com/s/qp8cxqttta4zi70/irregular_mask.zip?dl=0)


### Testing Set
The testing test covers different hole-to-image area ratios: (0.01, 0.1], (0.1, 0.2], (0.2, 0.3], (0.3, 0.4], (0.4, 0.5], (0.5, 0.6]. Each category contains 1000 masks with and without border constraints. In total, we have created 6 × 2 × 1000 = 12, 000 masks.

[NVIDIA Irregular Mask Dataset: Testing Set](https://www.dropbox.com/s/01dfayns9s0kevy/test_mask.zip?dl=0)


### Note and Reference

If you find the dataset useful, please consider citing this page directly shown below instead of the data-downloading link url:
```
@inproceedings{nv_irregular_maskdata,
  title={NVIDIA Irregular Mask Dataset},
  author={Guilin Liu and Fitsum A. Reda and Kevin J. Shih and Ting-Chun Wang and Andrew Tao and Bryan Catanzaro},
  booktitle = {https://nv-adlr.github.io/publication/partialconv-inpainting}, 
  year={2018},
}
```

To cite our paper, please use the following:
```
@inproceedings{liu2018partialinpainting,
   author    = {Guilin Liu and Fitsum A. Reda and Kevin J. Shih and Ting-Chun Wang and Andrew Tao and Bryan Catanzaro},
   title     = {Image Inpainting for Irregular Holes Using Partial Convolutions},
   booktitle = {The European Conference on Computer Vision (ECCV)},   
   year      = {2018},
}

```


## Related Project

[Partial Convolution based Padding](../publication/partialconv-padding)

## FAQ

- How Equation (1) and (2) are implemented?
	- I implemented by extending the existing Convolution layer provided by pyTorch. 

		The general idea is that: 
	- we will have convolution operator C to do the basic convolution we want; it has W, b as the shown in the equations. for computing sum(M), we use another convolution operator D, whose kernel size and stride is the same with the one above, but all its weights are 1 and bias are 0.
	- Note: M has same channel, height and width with feature/image. M is multi-channel, not single-channel.
	- Thus C(X) = W^T * X + b, C(0) = b, D(M) = 1 * M + 0 = sum(M), W^T* (M .* X) / sum(M) + b = [C(M .* X) – C(0)] / D(M) + C(0) 


- The value of W^T* (M .* X) / sum(M) + b may be very small.
	- If you feel the value W^T* (M .* X) / sum(M) is too small, an alternative to W^T* (M .* X) / sum(M) + b is W^T* (M .* X) * sum(I) / sum(M) + b , where I is a tensor filled with all 1 and having same channel, height and width with M. Mathematically these two are the same. However, for some network initialization schemes, the latter one may be easier to train.

- How mask dataset is generated?
	- The mask dataset is generated using the forward-backward optical flow consistency checking described in this paper. The first step is to get the forward and backward flow using some code like deepflow or flownet2; the second step is to use theconsistency checking code to generate mask. Later, we use random dilation, rotation and cropping to augment the mask dataset (if the generated holes are too small, you may try videos with larger motions).


- What are the scale of VGG feature and its losses?
	- Be careful of the scale difference issues. The VGG model pretrained on pyTorch divides the image values by 255 before feeding into the network like this; pyTorch's pretrained VGG model was also trained in this way. This is what we are currently using. However, other framework (tensorflow, chainer) may not do that. It will have a big impact on the scale of the perceptual loss and style loss. 

- How to do padding?
	- Note that we didn't directly use existing padding scheme like zero/reflection/repetition padding; instead, we use partial convolution as padding by assuming the region outside the images (border) are holes. This will help to reduce the border artifacts. An easy way to implement this is to first do zero padding for both features and masks and then apply the partial convolution operation and mask updating. Details can be found here: [Partial Convolution based Padding](../publication/partialconv-padding)


- How skip link works?
	- For skip links, we do concatenations for features and masks separately. Assume we have feature F and mask output K from the decoder stage, and feature I and mask M from encoder stage. We do the concatenation between F and I, and the concatenation between K and M. The concatenation outputs concat(F, I) and concat(K, M) will he feature input and mask input for next layer.


- L1 loss
	- The L1 losses in the paper are all size-averaged. 



<!-- <video width="640" height="480" controls>
  <source src="https://drive.google.com/uc?export=view&id=11_WDpit5ETTXwZx5g4LmnkB0elVdiXT4" type="video/mp4">Your browser does not support the video tag.
</video>
 -->

