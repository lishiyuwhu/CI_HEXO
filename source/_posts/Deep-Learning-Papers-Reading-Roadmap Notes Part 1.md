---
title: Deep-Learning-Papers-Reading-Roadmap Notes Part 1
date: 2017-08-02 13:27:54
tags:
---

https://github.com/songrotek/Deep-Learning-Papers-Reading-Roadmap


# 1.1 Survey

[1] LeCun, Yann, Yoshua Bengio, and Geoffrey Hinton. "Deep learning." Nature 521.7553 (2015): 436-444. [pdf] (Three Giants' Survey) ⭐️⭐️⭐️⭐️⭐️

<!-- more -->

## NatureDeepReview

传统的机器学习局限于抽feature.

Representation learning, 表征学习可以让机器从原始数据出发自动得出representation.
DL把它转化到了一个更加抽象的level. 

### Supervised learning
监督学习是机器学习最通常的形式. 然后举了一个图片分类的例子, 然后采用梯度法去改变weight.
实作时, 用SGD. 这里'随机'的意思是, 每一个小的set of example都对整个集上面的平均梯度给出了noisy estimate. 这里我的理解就是, 用抽样的表现去拟合整个样本的表现, 也导致了每次迭代并不一定是向着整体最优方向去的.

线性分类器的效果不好. 这个shadow分类器就依赖于特征提取.为了改进分类器, 有人瞄上了通用的非线性feature, 也就是核方法.

### Backpropagation to train multilayer architectures
先简述了一下BP算法的思想, 也就是链式法则嘛. 然后说了下激活函数, sigmoid和relu.
然后是local minima和saddle point.

### Convolutional neural networks
1D for signals and sequences, including language; 2D for images or audio spectrograms, and 3D for video or volumetric images.
所谓的1d和2d.

4个key idea: local connections, shared weights, pooling and the use of
many layers

### Image understanding with deep convolutional networks

吹一波CNN

### Distributed representations and language processing

word2vector

### Recurrent neural networks

basic RNN -> LSTM, 有了记忆了, 聪明的智商又能占领高地了.

### END
总的来说, 这篇主要是在讲这一二十年的历史, 基本上都在课里或者书本里说过, 读着是没什么意思. 不知道历史的可以看看, 知道的权当系列的开胃小菜了.

---

# 1.2 Deep Belief Network(DBN)(Milestone of Deep Learning Eve)

对于DBN和RBM我是拒绝的.

# 1.3 ImageNet Evolution(Deep Learning broke out from here)

[4] Krizhevsky, Alex, Ilya Sutskever, and Geoffrey E. Hinton. "Imagenet classification with deep convolutional neural networks." Advances in neural information processing systems. 2012. [pdf] (AlexNet, Deep Learning Breakthrough) ⭐️⭐️⭐️⭐️⭐️

##ImageNet Classification with Deep Convolutional Neural Networks

看一下标题, 看一下作者, 就能感觉到这篇paper的重量.

