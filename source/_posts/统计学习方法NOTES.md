---
title: 统计学习方法NOTES
date: 2017-07-23 08:54:31
tags: 
- 统计学习方法
- NOTES
---

Model | Strategy | Algorithm

结构风险: $R_{srm} = \frac{1}{N}\sum^N_{i+1}L(y_i,f(x_i)) + \lambda J(f)$
**其中,$J(x)$体现了对复杂模型的惩罚**, 最大后验就是结构风险最小化的一个例子.

从贝叶斯角度看, 正则化项对应于模型的先验, 可以假设复杂的模型具有较大的先验, 简单的模型具有较小的先验.

<!-- more -->

## 验证
一般K-fold cross-validation, 样本少的时候Leave-one out cross-validation.
注意还有Bootstrap方法
>（二）Bootstrap
是一种有放回的抽样统计过程（with replacement）.
条件：样本足够多，致使抽样能够体现原始样本。
核心思想：从一个dataset set有放回的抽样，形成training set。
0.632 bootstrap：n个实例的数据集，被有放回的抽样n次，每次仍包含n个实例。由于是有放回抽样，所以在新的训练集中可能会包含重复实例，原始数据中那些未被选择的实例就作为测试实例。
数据集中一个实例未被选择的概率为（1-1/n）*…(1-1/n)=e-1=0.368 (e=2.7183，是自然对数)
上面数据说明了在一个较大的数据集中，训练集中可能含有63.2%的原始数据，而测试集中含有36.8%的实例。
bootstrap过程：e=0.632*e（test-instance）+0.368*e（trainning-instances）
优点：是小数据集的错误率估计的最好办法；
缺点：一个随机的数据集，被等量分成两类，因此真实的错误率为0.5。

生成模型与判别模型, 大致上:
生成模型告诉你分布, 判别模型告诉你是啥. 虽然生成模型最后也会进行判别, 但这是由分布来的.



## 感知机（perceptron）

其实学过SVM之后, 再去看感知器就感觉很清晰了, 就是一个二分类的超平面.

![感知器](http://otivusbsc.bkt.clouddn.com/65b3c3cd-8dd4-4a4b-b3db-536c52f1a302)

来看对偶形式
![图片描述](http://otivusbsc.bkt.clouddn.com/2f072424-9220-41dd-96e0-622cb5ae9d66)
![图片描述](http://otivusbsc.bkt.clouddn.com/57ffbec6-4a3b-48f2-8e3c-f46d1ea538c2)

代码也写着很简单


