## [A MultiPath Network for Object Detection](http://120.52.73.75/arxiv.org/pdf/1604.02135.pdf)

## 概述

文章的框架图如下图所示，对于一张测试的图片，首先整图丢进卷积层得到[各层的featmap]()，然后对于图片上[每个proposal]()，用roi pooling layer提取多个层的feature进行卷积层的特征融合（即skip connections）。并且，针对每个proposal，将它的ROI放大1倍，1.5倍，2倍，4倍，并且用ROI pooling layer来得到定长特征并且分别喂到两个fc层（即foveal structure）。之后fc层出来的得到的特征再concat到一起。那么每个proposal就得到一个4096x4的超长特征，然后接分类和定位的loss，这里的分类是针对MS COCO的评测标准来设计的loss，称为[integral loss]()。

<p align="center"><img src="http://7j1yz3.com1.z0.glb.clouddn.com/小Q截图-20160408174427.png" width="600" ></p>

> 下文分成三段，分别对应上述三个加粗过的核心部件。

## Skip connections

看来现在Skip connections都成了解决小物体问题的标配了，为何它能够解决在之前的文章中说过了，这里不再展开。
现在笔者看过几篇近期的文章，基本都用了这个trick，看来这个trick以后会成为CNN的标配？这也是很合理的，因为这个trick能够提取到较为精细的feature，同时引入的计算量又很少，确实很solid。

## Foveal structure

这个取名比较高大上啊，foveal是专指视网膜的中央凹（有道词典说的~）。但其实motivation也很直观，就是用不同的尺度去“看”同一个物体，比如在框架图中，用了4个尺度看一头牛，第一头牛比较大，context信息较少，第四头牛最小，同时context也很多。同时认为这些信息之间是可以互补的，如果都提取出来然后在concat到一起，特征的判别性就更强了。

如果有留意detection的同学可能会觉得其实文章的框架图有点眼熟。是不是有点像是multi-region network？（笔者之前也写过multi-region network的笔记，见这里）。确实，它们是有着不少相似之处（竟然连选的样例图片都同一头牛，这是要致敬吗？233~）。但相对于multi-region，这篇文章有两点不同：
1. motivation不一样，multi-region认为的是part之间可以互补（所以设置了左半边，右半边等region），这篇文章认为起作用的是“用不同的尺度去看同一个物体”（可能也是foveal这名字的来源）。
2. multi-region是基于SPP来做的，而这篇是基于Fast R-CNN做的，所以可以end-to-end地训练。

## Integral loss

这个loss的motivation是因为COCO数据集不仅要比较IoU要求大于50时候的AP，还还求对比IoU要求大于0.7甚至0.9的时候的AP。如果只有一个分类的loss，比如针对IoU大于0.5的，那么其实对于IoU大于0.9时候就不是最优的，所以，本文就连接多个分类的loss，这些loss的不同之处在于要求的IoU不一样，比如一个proposal跟gt的IoU是0.6，对于框架图中的第一个loss就看作正样本，对于最后一个loss就是负样本了。用这些分类loss训练之后，对于同一个proposal能够得到多个分类的置信度，加起来就是网络最终给予这个proposal的分类置信度。

但其实这个loss略为鸡肋。文章的作者也提到了，用IoU大于0.7训练的loss训练的网络，对于IoU要求0.5时候就不是最优的了，所以这个loss是针对MS COCO的metric是好的，但是用其他metric的话，效果可能反而会差些。所以还是得因地制宜地用这个loss。
