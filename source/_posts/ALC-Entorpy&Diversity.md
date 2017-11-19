---
title: ALC-Entorpy&Diversity
date: 2017-09-16 16:03:18
tags: ALC
---

[Active Batch Selection via Convex Relaxations with Guaranteed Solution Bounds](http://ieeexplore.ieee.org/abstract/document/7006697/)

提出了entropy和diversity这两个用来判断hard sample的概念.

**想法:
	每一次挑选一个最优样例, 再去训练model, 这样为循环的挑选样例, 真的会是全局最优的解吗? 这样贪心, 会是最优吗..........考虑一下贪心失效情形?
**

相关

[An Active Learning Approach with Uncertainty, Representativeness, and Diversity](https://www.hindawi.com/journals/tswj/2014/827586/)

entropy: However, in multiclass problems, the entropy
does not often well reflect the uncertainty of the sample.Some may have larger classification uncertainty than the
ones whose entropy may be higher


QBC: QBC algorithms train a committee of classifiers and
choose the instance on which the committee members most
disagree. In essence, the QBC is also based on uncertainty
sampling.  

1.Uncertainty Measure. 

BvSB, best vs. second best. ![图片描述](http://otivusbsc.bkt.clouddn.com/88396476-4d79-40bd-bdda-f557f5c32019)
问题来了: outliers, 解决方法->representativeness sampling. 
	
2. Representativeness Measure. 高代表性的sample更小可能会是一个outlier. 使用Gaussian Process. measure the representativeness information between the current sample and the remaining unlabeled sample set.
![图片描述](http://otivusbsc.bkt.clouddn.com/41079c4f-6f62-476a-94e7-06e2bb4abfdd)

具体的Gaussian process也讲到了. 

3. Information Content Measure. 结合上面两点的长处. 
![图片描述](http://otivusbsc.bkt.clouddn.com/11b4987c-66ed-473f-bcaf-31101dd72c33)

直接乘, 也没什么道理.

4. Diversity Measure. 为了不选在同一个cluster的sample, 使用 kernel
𝑘-means clustering algorithm to cluster samples with high
information content. 

最后的算法:

![](http://otivusbsc.bkt.clouddn.com/6102387a-9afc-4425-97e4-cd26ba794e5d)


---

[Active learning combining uncertainty and diversity for multi-class image classification](http://ieeexplore.ieee.org/document/7108349/)

multiclass classification, 基于SVM, 使用了 OVO strategy(one vs. one), 任意两个类之间来一个classifier.


Uncertainty Measure, 一样也是BVSB.

Diversity measure, 使用Gaussian kernel来衡量similarity, 
![图片描述](http://otivusbsc.bkt.clouddn.com/d714f02c-5298-4b84-80fc-e6e7171bf192)
把$x$同每一个example $S_i$求高斯核取极大.
