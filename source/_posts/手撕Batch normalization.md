---
title: 手撕Batch normalization
date: 2017-11-22 15:00:09
tags:
---

## 前向传播

![图片描述](http://otivusbsc.bkt.clouddn.com/da60f00a-6508-431d-a79f-f4ba99911851)

## BP

![图片描述](http://otivusbsc.bkt.clouddn.com/d7565b1e-d863-4819-94a0-7308c9583728)

or, [Understanding the backward pass through Batch Normalization Layer](http://kratzert.github.io/2016/02/12/understanding-the-gradient-flow-through-the-batch-normalization-layer.html)

## train

真正train的时候, normalization过程并不是直接微分省略掉, 要把每个batch里面的东西全部考虑.

BP的时候, 还是从u和σ来传递到前面

## test

μ和σ都需要用一整个batch得到, 那怎么做test?

 - 使用整个training set的μ和σ
 - train过程中的每个epcho的μ全部"平均"起来当作μ.(快结束的weight比较大)
 
## Benefit

 - learning rate可以设大点, 更快
 - less exploding/vanishing gradients(especially effective for sigmoid,tanh)
 - less affected by initialization
 - reduces the demand for regularization(对抗 overfitting)