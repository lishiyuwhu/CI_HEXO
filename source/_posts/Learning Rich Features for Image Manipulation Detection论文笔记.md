---
title: Learning Rich Features for Image Manipulation Detection论文笔记
date: 2018-07-10 15:54:08
tags: 
---

<Learning Rich Features for Image Manipulation Detection>为cvpr18上Adobe的一篇文章，内容相关图像取证，PS照片识别。


使用一个双流Faster R-CNN 训练它来end-to-end的寻找伪造区域。其中一个为RGB流，来寻找RGB输入中的对比度差异，不自然的伪造边界等。另一个是一个噪声流，来放大从richmodel里得到的噪声特征。然后通过一个双线性pool来融合特征。

<!-- more -->

伪造的图像主要是三种：
splicing copy-move removal


image forensics的一些相关工作：CFA pattern, <Exploiting spatial structure for localizing manipulated image regions.>使用LSTM来寻找伪造区域的边界

![](http://otivusbsc.bkt.clouddn.com//3d0b5317-6d5d-44f6-b879-d4d6be77c7be)、


之前的伪造检测database太小，需要先用COCO来pretrain一下


our contribution:

 - 双流faster RCNN的使用
 - 双流模式是一个新的伪造检测模式
 

## RGB stream

一个resnet101来生成features和做分类

## noise stream

使用三个SRM核来捕捉噪声。 不做bonding box（因为相比RGB效果差很多）

## bilinear pooling 


https://blog.csdn.net/wayne2019/article/details/78441001

这部是来将RGB流和噪声流融合，使用Compact bilinear pooling 来做细粒度的分析

![](http://otivusbsc.bkt.clouddn.com//588af110-4512-4389-b068-f958cf5e124c)

