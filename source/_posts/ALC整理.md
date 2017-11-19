---
title: ALC整理
date: 2017-09-15 19:15:28
tags: 
 - NOTES
 - ALC
---

** 和CNN结合, 主动选择样本训练, 在训练放缓时?去active learning**

---

ALC应该分为三部分, A-选择样例, L-学习主体, C-选择annotator.

stream-based: 未标注样例逐个提交给样例选择部分, 由其决定是否需要标注. 

poo-baesd: 算法维护了一个未标注样例集, 从中挑选需要标注的样例.有几个方法方向:

 - 基于不确定度缩减. 信息熵作为依据, 信息熵大就意味着当前分类器难以正确分类, 也就是靠近分类边界的样本. loggistic regression和SVM都能很好的使用这个.
 
 - 基于版本空间缩减(reduction of version space). 分歧度作为依据. QBC(Query By Committee)算法, 从版本空间随机选择若干假设构成一个委员会, 然选择委员会中的假设预测分歧最大的样例进行标注. 评价分期度常用的由JS散度, KL散度, vote entropy. 为了优化委员会的构成, 增强其多样性, 可以采用bagging, Adaboost等成熟的的分类器集成算法从版本空间中产生委员会.
	cosine distance, 余弦相似度
	committee构成方法: 不同的model / ensemble methods
	
	有的直接对原始的noise label集成得到一个integrated dataset再去使用. 应该也可以属于这种.

![投票熵](http://otivusbsc.bkt.clouddn.com/d9678560-473d-4e6a-9405-959516f3d0fb)

 - 基于泛化误差缩减. 先选择一个lost function, 对每一个样例进行计算, 选择估计误差缩减最大的进行标注. 要针对不同的基准分类器提出相对应的算法, Naive Bayes, Bayesian net, kNN等. 这种方法直接针对分类器性能的最终评价指标, 理论上效果很好但计算量较大, 同时lost function的精度对性能也有很大影响
 - 其他手段. 预聚类( ~~😶又有了~~)

---

# 一些文章

[Self-Taught Active Learning from Crowds](http://ieeexplore.ieee.org/document/6413841/):

那些低精度的annotator可以从orcle给出的答案中学习知识


[Learning from crowds with active learning and self-healing](https://link.springer.com/article/10.1007/s00521-017-2878-y):

对于低精度的noise label, 先通过集成得到一个integrated dataset
寻找样本的方法是-- pool-based , 使用uncertainty estimation 
然后build m random decision trees, 

