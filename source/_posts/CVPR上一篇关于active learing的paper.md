---
title: CVPR上一篇关于active learing的paper
date: 2017-09-13 20:29:00
tags: ALC
---

这篇在OneNote里面躺了一个多月了, 今天得闲看看.

[Zhou_Fine-Tuning_Convolutional_Neural_CVPR_2017_paper](http://openaccess.thecvf.com/content_cvpr_2017/papers/Zhou_Fine-Tuning_Convolutional_Neural_CVPR_2017_paper.pdf)

[作者亲自写的简介](http://www.jianshu.com/p/42801f031cfa?utm_campaign=hugo&utm_medium=reader_share&utm_content=note&utm_source=weixin-friends)

---

首先作者说了两个情形:

>一共手头有100个未标记样本，和仅仅够标记10个样本的钱，老板说，通过训练这十个标记的样本，能接近甚至达到训练100个样本的performance；

>手头有了一个已经在100个样本中训练完的分类器，现在又来了100个新标记的样本，老板说，只给提供够训练10个样本的计算机；或者只给你提供够训练10个样本的时间，让分类器尽快学习到新来样本的特征；


<!-- more -->

为什么说不同呢? 不都是从少量样本得出特征吗?????

懂了, 第一种情况sample的label是未知的, 只有少量能够获得. 第二种是都知道标签, 但只能选择一小部分去更新学习引擎.

ALC的两个要点, 选择恰当的样本去训练, 选择恰当的annotator去判别. 挑样本该挑哪些呢, 直观地就是挑难以分辨的, 处于当前分类边界附近的. 两个指标, entropy和diversity. 来自[“Active batch selection via convex relaxations with guaranteed solution bounds”](http://ieeexplore.ieee.org/abstract/document/7006697/).
(这篇也要看看, 正好是我想问的)


然后和深度结合就是, 先把图片扔进自然图片的预训练网络中, 跑出来最难的, 标记, 再去训练网络, 重复过程.

#问题点: entropy还好说, diversity就很麻烦了#

这篇抓住了Data Augmentation这个点来解决, 给出了一个基本思想:

来自同一个image的patches, 从CNN跑出来的结果应该是一致的. 即那些跑出来不一致的样本, 这个image就是important的.

 - 我们虽然跑不出lable但能跑出来预测结果是否一致, 一致的话认为这是个easy simple就不去选择.  #就感觉没什么道理, 直接划分错大类该怎么说#
 - 结合data augmentation的优点是我们可以知道哪些patch对应什么image，比较容易控制。这样就可以在一个image內算diversity了，每个image对应一个矩阵，大小是一样的，非常的简洁，也容易控制计算量。(这部分还要去看下文章)


然后有个工作就是, 经过Data Augmentation的一组patches, 很可能有一部分就难以识别. 于是, 先判断分类大方向, 也就是以0.5为界限, 然后去取最大或者最小的那25%的patch.

所谓noisy lable:
![图片描述](http://otivusbsc.bkt.clouddn.com/6960c62c-cf80-415f-8ddb-60e148c5bb24)