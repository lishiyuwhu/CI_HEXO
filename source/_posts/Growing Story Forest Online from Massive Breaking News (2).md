---
title: Growing Story Forest Online from Massive Breaking News
date: 2018-11-12 9:30:20
tags: 
---

上学期做的一个作业


<!-- more -->

# PPT

![](https://markdown-1257721827.cos.ap-shanghai.myqcloud.com/幻灯片1.JPG)

![](https://markdown-1257721827.cos.ap-shanghai.myqcloud.com/幻灯片2.JPG)




# Growing Story Forest Online from Massive Breaking News

针对海量的、连续的新闻文本数据，进行话题检测与跟踪(TDT), 目标: online且迅速准确的在长时间流内抽取出独立的event与topic

现有方法: Event timelines, event threads, event evolution graphs, information maps. 这些方法在应用到海量数据时会有如下问题:
 - 难以以一个合适的粒度来抽取独立的事件
 - 可视化不便于使用者理解 
 - 使用的均为offline的算法. 但数据的持续添加需要online的算法

为此, 提出的Story Forest可以对大量长文本聚类为清晰的event, 然后将事件聚类为story--一个由事件组成的树. 
从大到小的结构层次是:topic → story → event → doc


# THE STORY FOREST SYSTEM

## Preprocessing 预处理

输入: 全部doc
目标: 抽取出每一篇doc中keyword

主要分为三部分:
 - Document fltering 直接过滤掉小于20个字符的文章
 - Word segmentation 词分割. Stanford Chinese Word Segmenter Version 3.6.0
 - Keyword extraction 关键词提取. GBDT+LR  

其中GBDT+LR部分, 采用的特征是一组混合特征:位置名称等三个词特征, TF-IDF TextRank等八个结构特征, 训练好的1000维LDA作为语义特征. 数据集采用手工标注的10000+ doc, 包含了20000+关键词. 当输入给gbdt的特征非常多时，GBDT其实是无法充分利用所有特征的(考虑树的深度和棵数), 使用GBDT+LR可以有效使用这些特征

> GBDT(Gradient Boost DecisionTree)是一种常用的非线性模型，它基于集成学习中的boosting思想，每次迭代都在减少残差的梯度方向新建立一颗决策树，迭代多少次就会生成多少颗决策树.GBDT的思想使其具有天然优势可以发现多种有区分性的特征以及特征组合，决策树的路径可以直接作为LR输入特征使用，省去了人工寻找特征、特征组合的步骤.GBDT+LR能寻找出有区分性特征（raw feature）、特征组合（cross feature），融合后直接通过黑盒子（Tree模型GBDT）进行特征组合的自动发现.从本质上来说，GBDT 是叶子节点之间的线性加权，GBDT + LR 则是将叶子节点转化成 One-Hot 特征后再进行广义线性加权.
 
## doc聚类

输入: 全部keyword
目标: 生成关键词共现图(keyword co-occurrence graph), 以此生成规模较小的子图(sub-graph
).

首先, 构建一个大的关键词共现图. 图中每一个节点都是一个event, 两个节点间的边要满足如下条件:
 - 共现次数>3
 - 出现的条件概率$Pr(\omega _i|\omega _j)>0.15$, $Pr(\omega _j|\omega _i)>0.15$

===

其次,采用社区发现(community detection)将网络中联系紧密的部分找出来, 将大的关键词图划分为一个个的关键词community, 生成规模较小的子图.

> 社区发现（Community Detection）算法用来发现复杂网络中的社区结构，也可以看做是一种聚类算法.社区内部联系稠密，而社区之间联系稀疏. 对每一条边采用介中性打分(betweenness centrality score).

====

输入: keyword community
目标: 将doc聚类为topic

计算每一个doc和keyword community之间的余弦相似度. doc特征使用TF-IDF, 而keyword community将其看作一个doc也使用TF-IDF. 把doc归为余弦相似度最高的keyword community内, 以此将doc按关键词聚类为topic.

====

## event抽取

最后,  将每个topic内的doc聚类为细粒度的event


输入: 一个topic内的doc
目标: 将doc聚类与event

训练一个SVM来判断两个doc是否描述的是同一个event. 采用一系列的document-pair features, 包括标题TF-IDF\TF向量的余弦相似度, 内容TF-IDF\TF向量的余弦相似度, doc第一句的相似度等. 

类似的采用社区发现算法, 节点为doc, 边定义为上述SVM的判断结果, 产生doc的community, 以此聚类为event.


经过这一段, 我们得到了topic <del>  → story </del> → event → doc
接下来, 来完成story部分.

## Growing Story Trees Online. 从event构建story tree

###  Identifying the related story tree.  event聚类为story

输入: 一个event, 现有的Story Forest(其中包含若干story tree)
目标: 将event归入相应的story tree 或者 新建一个新的story tree

采用关键词的相似度来衡量. 对于某个event, 其中全部doc的关键词组成的集$E$, 对于某个story tree, 类似的关键词集$S_i$, 然后采用Jaccard系数$C_i = \frac{|S_i\cap E|}{|S_i\cup E|}$作为衡量依据, 将该event分入$C_i$最大的story tree. 
只有$\forall S_i,$$C_i =0$时, 才新添加一个 story tree.

###  Updating the related story tree.   story tree的结构构建.

#### merge

重心文档centroid doc. 一个event的重心文档是与所有文档都相连的doc
 > Te centroid document of an event is simply the concatenation of all the documents in the event.

使用上面的SVM来判断新event的centroid doc和 story tree内各个event的centroid doc是否相似. 相似即merge

#### Extend and Insert 
 
新event$E$, 对应的story tree$S$, $S$内的每个event$E_j$
定义一个ConnectionStrength  $CS(E_j, E) = compatibility(E_j,E) \times coherence(S→E_j→E) \times timePenalty(E_j ,E)$

若$CS(E_j, E)$小于一个阈值(未提及具体数值), 在story tree的根节点上Insert.
否则选取使得$CS(E_j, E)$最大的$E_j$, 在其之上作Extend.

# 总结

>现有方法: Event timelines, event threads, event evolution graphs, information maps. 这些方法在应用到海量数据时会有如下问题:
 - 难以以一个合适的粒度来抽取独立的事件
 - 可视化不便于使用者理解 
 - 使用的均为offline的算法. 但数据的持续添加需要online的算法

最后来看 Story Forest如何解决这三个难点:
 - 先按关键词将doc划分为topic, 再在topic内将doc聚类为event
 - 采用树的结构, 清晰明了.
 - 采用往树中添加节点的方式, online的生成Story Forest.
 