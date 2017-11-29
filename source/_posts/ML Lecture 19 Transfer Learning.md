---
title: ML Lecture 19 Transfer Learning
date: 2017/11/28  15:40
tags: ML
---

什么是迁移学习? Data not directly related to the task considered

![](http://otivusbsc.bkt.clouddn.com/e3c3cf3f-a1d3-40b6-95ec-03dd39beff4b)

<!-- more -->

## Fine-tuning

最常见的就是Fine-tuning

用少的target data去fine-tune原来的model(target data极少的时候, 就是One-shot learning了)

一些trick:
 - Conservative Training
 train新model的时候加一些限制, 加一些"normalization". 比如加个L2范数和原模型不要差太多

 - Layer Transfer
 限制一些layer在训练中不改变参数. 这种方法是非常常见的作法
 对于图片: 一般取前面几层的layer -> 保留filter, 最基础的特征抽取
 对于语音: 一般取最后几层的layer -> 保留发声方式 声色等信息
 ![图片描述](http://otivusbsc.bkt.clouddn.com/e215c619-0003-4df4-8d05-0a2bb5f76103)
 两个红框说明了什么? 说明layer transfer的确是可行的, 固定住前三个layer再在后面layer作fine-tune, 可以达到与原data上类似的效果. (但是这里, target data可是和source data一个量级的)
 
## Multitask Learning
和finetune不同的是, 需要在target domain 和source domain都有好的表现, 而不是像finetune那样不管source只关心target domain

直观来讲就是这样的任务

![图片描述](http://otivusbsc.bkt.clouddn.com/8778ad28-e6a0-435e-9669-5c6887be462c)

更加深入的想法是, 
![图片描述](http://otivusbsc.bkt.clouddn.com/67b89301-d4ba-459d-8a00-45c6c560c6a2)
可以在中间公用一部分的layer

## Domain-adversarial training

target data没有label, 这就很excited了. 而且source data和target data是非常mismatch的
![](http://otivusbsc.bkt.clouddn.com/81457ee5-b8f0-4308-ad01-5763543649bb)

问题在于: 两拨data, 在经过CNN前几层抽取特征然后t-sne可视化之后, 可能根本就不会相邻, 两组data分的开开的, 那前几层filter就没法起作用了.

方法: 去先把domain的特性消除掉, 不同的domain应该混在一起 --> 那就接个Domain classifier去判断. 有一种GAN的感觉

## Zero-shot Learning

![图片描述](http://otivusbsc.bkt.clouddn.com/41edec2b-da20-418b-95e1-23abec65541d)
~~为什么大家都喜欢用羊驼~~

方法: 把每一个class都用一系列的attribute表示

