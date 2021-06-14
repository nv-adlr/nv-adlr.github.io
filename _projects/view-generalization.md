---
title: "View Generalization for Single Image Textured 3D Models"
collection: publications
permalink: view-generalization
excerpt: ''
date: 2021-06-13
venue: 'CVPR 2021'
paperurl: ''
citation: 'Anand Bhattad, Aysegul Dundar, Guilin Liu, Andrew Tao, Bryan Catanzaro, View Generalization for Single Image Textured 3D Models, Proceedings of IEEE Computer Society Conference on Computer Vision and Pattern Recognition(CVPR) 2021.'
---

![image info](images/view-generalization-teaser.gif)



## Abstract
Humans can easily infer the underlying 3D geometry and texture of an object only from a single 2D image. Current computer vision methods can do this, too, but suffer from view generalization problems - the models inferred tend to make poor predictions of appearance in novel views. As for generalization problems in machine learning, the difficulty is balancing single-view accuracy (cf. training error; bias) with novel view accuracy (cf. test error; variance). We describe a class of models whose geometric rigidity is easily controlled to manage this tradeoff. We describe a cycle consistency loss that improves view generalization (roughly, a model from a generated view should predict the original view well). View generalization of textures requires that models share texture information, so a car seen from the back still has headlights because other cars have headlights. We describe a cycle consistency loss that encourages model textures to be aligned, so as to encourage sharing. We compare our method against the state-of-the-art method and show both qualitative and quantitative improvements.

## Video
<iframe width="853" height="480" src="https://www.youtube.com/embed/4mU6Mb8hWpg" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Code
[Coming Soon]()

## Sample Results
{::nomarkdown}
<table>
<tr>
 <td><img src="images/view-generalization-sample_results/Input/img_175.png" width=256 \><p align="center"><center>Input</center></p></td><td><img src="images/view-generalization-sample_results/DIBR/img_175.gif" width=256 \><p align="center"><center>DIBR</center></p></td><td><img src="images/view-generalization-sample_results/ours_baseline/img_175.gif" width=256 \><p align="center"><center>Ours (baseline)</center></p></td><td><img src="images/view-generalization-sample_results/ours_final_ganv1/img_175.gif" width=256 \><p align="center"><center>Ours (final)</center></p></td></tr>
<tr>
<td><img src="images/view-generalization-sample_results/Input/img_3.png" width=256 \><p align="center"><center>Input</center></p></td><td><img src="images/view-generalization-sample_results/DIBR/img_3.gif" width=256 \><p align="center"><center>DIBR</center></p></td><td><img src="images/view-generalization-sample_results/ours_baseline/img_3.gif" width=256 \><p align="center"><center>Ours (baseline)</center></p></td><td><img src="images/view-generalization-sample_results/ours_final_ganv1/img_3.gif" width=256 \><p align="center"><center>Ours (final)</center></p></td></tr>
<tr>
<td><img src="images/view-generalization-sample_results/Input/img_33.png" width=256 \><p align="center"><center>Input</center></p></td><td><img src="images/view-generalization-sample_results/DIBR/img_33.gif" width=256 \><p align="center"><center>DIBR</center></p></td><td><img src="images/view-generalization-sample_results/ours_baseline/img_33.gif" width=256 \><p align="center"><center>Ours (baseline)</center></p></td><td><img src="images/view-generalization-sample_results/ours_final_ganv1/img_33.gif" width=256 \><p align="center"><center>Ours (final)</center></p></td></tr>
<tr>
<td><img src="images/view-generalization-sample_results/Input/img_53.png" width=256 \><p align="center"><center>Input</center></p></td><td><img src="images/view-generalization-sample_results/DIBR/img_53.gif" width=256 \><p align="center"><center>DIBR</center></p></td><td><img src="images/view-generalization-sample_results/ours_baseline/img_53.gif" width=256 \><p align="center"><center>Ours (baseline)</center></p></td><td><img src="images/view-generalization-sample_results/ours_final_ganv1/img_53.gif" width=256 \><p align="center"><center>Ours (final)</center></p></td></tr>
<tr>
<td><img src="images/view-generalization-sample_results/Input/img_85.png" width=256 \><p align="center"><center>Input</center></p></td><td><img src="images/view-generalization-sample_results/DIBR/img_85.gif" width=256 \><p align="center"><center>DIBR</center></p></td><td><img src="images/view-generalization-sample_results/ours_baseline/img_85.gif" width=256 \><p align="center"><center>Ours (baseline)</center></p></td><td><img src="images/view-generalization-sample_results/ours_final_ganv1/img_85.gif" width=256 \><p align="center"><center>Ours (final)</center></p></td></tr>
<tr>
<td><img src="images/view-generalization-sample_results/Input/img_131.png" width=256 \><p align="center"><center>Input</center></p></td><td><img src="images/view-generalization-sample_results/DIBR/img_131.gif" width=256 \><p align="center"><center>DIBR</center></p></td><td><img src="images/view-generalization-sample_results/ours_baseline/img_131.gif" width=256 \><p align="center"><center>Ours (baseline)</center></p></td><td><img src="images/view-generalization-sample_results/ours_final_ganv1/img_131.gif" width=256 \><p align="center"><center>Ours (final)</center></p></td></tr>
<tr>
<td><img src="images/view-generalization-sample_results/Input/img_215.png" width=256 \><p align="center"><center>Input</center></p></td><td><img src="images/view-generalization-sample_results/DIBR/img_215.gif" width=256 \><p align="center"><center>DIBR</center></p></td><td><img src="images/view-generalization-sample_results/ours_baseline/img_215.gif" width=256 \><p align="center"><center>Ours (baseline)</center></p></td><td><img src="images/view-generalization-sample_results/ours_final_ganv1/img_215.gif" width=256 \><p align="center"><center>Ours (final)</center></p></td></tr>
</table>
{:/}

## Authors
{::nomarkdown}
<table>
<tr>
<td><img src="images/view-generalization-authors/anand.png" width=256 \><p align="center"><center><a href="https://anandbhattad.github.io/">Anand Bhattad</a></center></p></td><td><img src="images/view-generalization-authors/ayse.jpeg" width=256 \><p align="center"><center><a href="http://www.cs.bilkent.edu.tr/~adundar/">Aysegul Dundar</a></center></p></td><td><img src="images/view-generalization-authors/guilin.jpeg" width=256 \><p align="center"><center><a href="https://liuguilin1225.github.io/">Guilin Liu</a></center></p></td><td><img src="images/view-generalization-authors/andrew.png" width=256 \><p align="center"><center><a href="https://scholar.google.com/citations?user=Wel9l1wAAAAJ&hl=en">Andrew Tao</a></center></p></td><td><img src="images/view-generalization-authors/bryan.jpeg" width=256 \><p align="center"><center><a href="https://ctnzr.io/">Bryan Catanzaro</a></center></p></td></tr>
</table>
{:/}

## Citation
```
@article{Bhattad2021View,
author = {Anand Bhattad and Aysegul Dundar and Guilin Liu and Andrew Tao and Bryan Catanzaro},
title = {View Generalization for Single Image Textured 3D Models},
journal = {arXiv},
year = {2021},
}
```
## Acknowledgements
We thank David A. Forsyth for insightful discussions. We also thank Yuxuan Zhang and Wenzheng Chen for providing us baseline code of DIBR. 