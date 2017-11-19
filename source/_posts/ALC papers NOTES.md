---
title: ALC papers NOTES
date: 2017-08-06 15:47:31
tags: ALC
---

~~感冒又在床上躺了几天, 该起来干活了.~~


# Active Learning from Crowds with Unsure Option
Jinhong Zhong , Ke Tang  and Zhi-Hua Zhou

## Abstract

Learning from crowds, where the labels of data instances are collected using a crowdsourcing way,
和传统众包做法把所有数据都分发出去作标注不同, active learning from crowds主动地只选取一部分数据分发出去标注, 减少了标注的cost.本文更进一步的, 允许标注者回答'unsure'. 减轻了标注者的负担, 更能提高准确率

<!-- more -->

## 1 Introduction

采用了多个标注者之后, ALC算法不仅要选择去查询哪些实例, 同时还要选择为选定的实例去寻找恰当的标注者,  is a non-trivial task. 于是, 衡量候选者所具有的专业知识(expertise), 也就成为了算法的关键. 
Active Learning from Crowds with Unsure option (ALCU),

## 2 Related Work

算法关键是如何去衡量标注者的expertise, 现有的方法有三类:

1. 一个标注者以一个确定的概率正确回答, 然后以这个概率作为所有标注者对所有实例真却回答的概率
2. 一个标注者正确标注一个实例由两方面决定, 标注者自己的可靠性以及该实例的难易程度.
3. 直接估计每个标注者对于每个实例能够做出正确标注的概率, but estimating the accuracy of each annotator in each instance, which is #non-trivial# to solve.

## 3 Problem Description and Analyses

## 4 ALCU-SVM

有几点:

 - 训练初期, 先去掉正则项或者说是限制项, 因为此时即使存在标注者能够正确标注也不一定能够选择出来.
 - 标注者可靠性模型的构建, 是一个类别不均衡问题(class-imbalanced). -> set weights proportional

## 5 Experiments

## 6 Conclusion and Discussion
'unsure'选项能够显著的优化ALC的结果.

有几个进一步的方向:

 - ALCU-SVM仅仅依赖于标注者的反馈来判断一个标注者能否可信, 当标注者falsely confident时会有不好的表现. 我们可以再建立一个能够反映标注者信心的模型, 另一方面, 令模型更加鲁棒, 能够对错误的标注有一定容忍程度.
 - softmax function的使用
 - 最优标注者的选择
 - 当有大量标注者时, 算法的scalability
 
 
# References

# Finite-time Analysis of the Multiarmed Bandit Problem


## Abstract
强化学习中的一个困境, 需要在exploring和按经验的最佳行动之间找寻一个平衡.
一个简单的例子就是多臂赌博机, 'regret'可以有很好的效果

所谓多臂赌博机, 

>简短来说，在合适的假设下，我们可以保证，存在这样一种算法，使得我们的“后悔程度”是最低的。譬如，当一名赌徒面对近似为（静态）随机过程的老虎机时，他需要一种策略，最大化他自己的利益。再形象一点：赌徒进了赌场，面对一千台机器，每台机器的中奖率是一个定值。当然，赌徒不知道中奖率的数值，他只能靠不断赌博来测试每台机器的赢面。测得越多，对中奖率的估计就越准。那么，赌徒究竟是应该何时到后面没试过的机器碰碰运气？何时应该继续在35号机上继续下注（因为刚才在35号下注三次都赢了）？抑或换回一开始的2号机（也许2号机的赢面最大，只是赌徒一时运气不好输掉了几轮）？
作者：Filestorm
链接：https://www.zhihu.com/question/19784847/answer/12953467

文中提出的策略, 不只是渐进的, 而是一致的达到logarithmic regret





#  Multiple-Instance Active Learning

[Settles et al., 2008] B. Settles, M. Craven, and S. Ray.
Multiple-instance active learning. In NIPS, pages 1289–
1296, 2008.

---

```

multiple-instance (MI)问题, 实例们被分割成bags, 而不再是独立的实例. 对于ML问题来说,每个包都有一个训练标记，而包中的示例是没有标记的；如果包中至少存在一个正标记的示例，则包被赋予正标记；而对于一个有负标记的包，其中所有的示例均为负标记。（这里说包中的示例没有标记，而后面又说包中至少存在一个正标记的示例时包为正标记包，是相对训练而言的，也就是说训练的时候是没有给示例标记的，只是给了包的标记，但是示例的标记是确实存在的，存在正负示例来判断正负类别）

关于多示例问题怎么求解，假如说所有的样本标记都已经知道了，那就是一个监督学习的问题了，用SVM，adaboost之类的都可以做。现在的困难是，有很多样本的标记我们不知道。对于负样本包来说就无所谓了，里面每个样本那都是负标记，这个是明确的。问题出在正样本包上面，每个正样本包里只能保证有一个是正样本，其他的是正是负就不知道了，关键是到底是哪个样本是正的呢？这个也是不清楚的。

解决这个问题的方法其实挺直接的：迭代优化（alternative optimization)。也就是说，我们先假设已经知道了所有样本的标记，那么就可以通过某种监督学习的方法得到一个分类模型，通过这个模型我们可以对每个训练样本进行预测，然后更新它们的标记，我们又可以拿这一次新得到的标记重新训练分类模型了。所以整个优化过程分为两部分：监督学习，标记更新。

这里还有一些地方需要注意：

第一点， 训练监督学习的模型的时候，只从正样本包里挑选被预测的“最像正确”(也就是分类得分最高)的那一个，正样本包里面其他的样本，不管预测出来是正的还是负的都不要了。这是因为，其实多示例的问题也可以描述为，正样本包里面“最正确”的一个样本标记是正的，跟其他样本无关。所以，这种选择策略恰恰是符合问题定义的。

第二点，如果负样本足够多的话，可以只挑选每个负样本包里面被预测“最像正确"的一个样本作为负样本进行训练，这样子的负样本也叫做hard sample或者most violated sample。实践上来说，它们对于模型快速收敛是最有效的。

```

---

#  Active Learning Using Pre-clustering

[Nguyen and Smeulders, 2004] H. T. Nguyen and A. Smeul-
ders. Active learning using pre-clustering. In ICML,
page 79. ACM, 2004.

---

通常的active learing收集数据的方法是选择那些靠近分类边界的实例, 文中提出了将聚类通active learing相结合的方法.

这个算法首先在聚类代表上构造了一个分类器, 然后将分类结果通过噪音模型传播到其他样本. 这个算法能够选择最有代表性典型的的例子, 而且能避免在相同的聚类中重复的打标签. 在学习过程中, 聚类由粗到细, 以期望能平衡好聚类规模和标签的准确性.

近年来, 半监督学习, 以及在少量打标初始数据身上改善监督学习很火. 许多方法改进监督学习通过使用未打标数据, 另一些在找寻好的策略来选择合适的数据来打标.
,也就是active learing.

现今active learning技术可以按照底层的学习方法分类, 像naive Bayes, SVM. 
naive Bayes分类器有两个问题,.首先, 分类器假设了feature的独立, 通常来讲这太强了. 其次, naive Bayes是一个基于likelihood估计的生成模型, 在active learing中, 由于data不是随机选取的, 这个估计通常是不准确的.

要点是去选择最有价值的训练数据. 许多算法选取的是最靠近分类边界也就是最难以确定的sample, 我们把它叫做closest-to-boundary准则. 一些其他的准则来自于SVM, 选择那些能够最大程度减小margin的例子.

closest-to-boundary方法忽略了对active learing很有帮助的先验 data distribution. 
本文中, 聚类的信息在两个方面上帮助了active Learning: 首先, 位于聚类中心的代表sample更加重要, 应该优先选择去打标; 其次, 位于同一聚类的sample可能应该具有相同的标签, 这样可以显著减少打标数量.


#  Active Learning by Querying Informative and Representative Examples

[Huang et al., 2010] S.-J. Huang, R. Jin, and Z.-H. Zhou.
Active learning by querying informative and representa-
tive examples. In NIPS, pages 892–900, 2010.

---

这篇是周志华老师的, 想想我也是看着西瓜书才算正式入门的.

大多数active learning方法选择informative或者representative的未打标实例去查询它们的标签. 虽然已经有算法将这两者结合起来查询, 但他们通常是ad hoc(点对点,特别的)的去找又informative又representative.

本文, 从一个给定的池中选择一个未打标实例去手动打标, pool-based基于池化的active learning. 两个主要的准则, informativeness & representativeness. informativeness衡量了一个实例减少统计模型不确定性的能力, 而representativeness衡量了一个实例是否能够很好的代表未打标数据整体pattern的能力. 大多数active learning算法都只关注了其中的一点: 关注informativeness通常不能利用未打标数据的结构信息, 产生bias, 得到不好的performance; 关注representativeness, 需要在得出最优分类边界之前就查询大大大大大量的实例. 

QUerying Informative and Representative Examples -> QUIRE method
基于min-max view of active learning. 衡量了一个实例的 informativeness and representativeness, informativeness由基于已打标数据的估计,  representativeness由基于未打标的数据估计.

所谓informativeness & representativeness

![](http://otivusbsc.bkt.clouddn.com/4e3b9e6f-b5bb-45bd-874e-507320cf0157)

具体来说

![](http://otivusbsc.bkt.clouddn.com/c15e4831-2afa-40d1-bc19-5fd6289df08e)


---

#  Toward optimal active learning through monte carlo estimation of error reduction

>Existing active learning approaches can be categorized as using a single annotator [Roy and McCallum, 2001] or using multiple annotators [Dekel et al., 2012].

[Roy and McCallum, 2001] N. Roy and A. McCallum. To-
ward optimal active learning through monte carlo estima-
tion of error reduction. ICML, 2001.

---

传统的监督学习中, 无论是什么样的training data都去用来train parameters. 与之相反, active learning自由的去选择哪些数据能加入进training set中. 一个active learning算法通常从很少的打已标数据开始, 小心的选择需要打标的数据, 从这些之中得到结果, 在使用新得到的知识去寻求下一步打标的数据. 这样, 就能期望以一个小的labled set得到好的performance. 尤其是, 当未打标数据cheap & plentiful, 打标这件事又艰难而且花时间.

这篇paper提出的方法是, 以最小化未来测试集上的error作为最优准则, 但是, 使用蒙卡估计去解决practicality problem. future error rate的估计,  either by log-loss,using the entropy of the posterior class distribution on a sample of the unlabelled examples, or by 0-1 loss, using the posterior probabilities of the most probable class for the sampled unlabelled examples.

每轮之后, 选取一个sample去打标, 然后再去估计future error rate

仅仅添加一个sample的训练, 其实可以是很有效的, 想一想SVM就能明白

---

# 5. Selective sampling and active learning from single and multiple teachers

>Existing active learning approaches can be categorized as using a single annotator [Roy and McCallum, 2001] or using multiple annotators [Dekel et al., 2012].

[Dekel et al., 2012] O. Dekel, C. Gentile, and K. Sridharan.
Selective sampling and active learning from single and
multiple teachers. JMLR, 13(1):2655–2697, 2012.

---
这篇有40+页 🤦🤦🤦🤦🤦🤦🤦🤦暂时先看个大概吧

一个新的online learning algorithm(叫在线学习怎么感觉这么别扭)选择sampling的框架, 其中label在揭示之前必须被主动查询过(where labels must be actively queried before they are revealed).对regret,以及查询lable的数量(when faced with an adaptive adversarial strategy of generating the instances)做了限制. 多教师情形下, 
由人手工生成label是很expensive的. active learning的范式是建立这样一个概念: 我们应该只去取得那些能够切实的改进prediction的label. 

online selective sampling:  a repeated game between a learner and an adversary. ~看了这个online selective sampling setting的概念, 有点GAN的感觉~
在第$t$轮, adversary给learner一个实例$x\_t$, 然后learner回应一个预测的二分类label$-1 ,+1$. learner和一个知道每个实例正确label的teacher相连接. learner必须要去决定是否去花费单位cost去向teacher查询正确的label. 如果learner决定去查询,  就能得到正确label然后改进以后的预测. 当我们要去衡量预测的accuracy时, 我们要考察所有的label, 无论是否被查询过. learner有两个目标: 精准预测和少量查询

### The Single Teacher Case

我们的假设只有$||x|| <= 1$, 而之前的工作都需要有一个很强的假设.

The single teacher algorithm is a margin-based selective sampling procedure

### The Multiple Teacher Case

K个teacher, 每个都有自己不同的expertise, 而算法并不知道teacher具体的情况, 只能够从teacher提供的label中推断. 经过一系列判断, 在面对新的查询时, 算法要决定把这个查询发送给哪些teacher更为合适. 注意这里, teacher提供的仅仅是一个二分类label, 而不是推断reliability rate. 虽然这样看起来更加可靠.
~~那为啥不去这么做啊~~
每个teacher都有一个隐藏的confidence

现在, 就不太容易去衡量learner的performance了, 因为K个teacher给出的结果有正有负, 就设定一个confidence的限界, 只选取那些有expertise而足够自信的teacher.

---

# Ensemble methods: foundations and algorithms

> The former is regarded as traditional active learning and the latter, which is essentially a kind of ensemble method [Zhou, 2012].(指的是多教师))

[Zhou, 2012] Z.-H. Zhou. Ensemble methods: foundations
and algorithms. CRC Press, 2012.

集成方法论, 一本大部头, 其中boosting和bagging两章找时间看看吧

---

# Active Visual Recognition with Expertise Estimation in Crowdsourcing

> Hence, estimating the expertise of candidate annotators plays a key role
in ALC algorithms. So far, much effort has been made toward
this purpose [Long et al., 2013] [Yan et al., 2011].

[Long et al., 2013] C. Long, G. Hua, and A. Kapoor. Ac-
tive visual recognition with expertise estimation in crowd-
sourcing. In ICCV, pages 3000–3007. IEEE, 2013.

---

虽然通过众包可以很cheap的大量得到labeled的数据, 但收集到的lable couldbe very noisy. 所以需要对打标者expertise进行衡量. 高水平的打标者更不容易产生label noises

之前的关于打标者expertise建模主要分两种:
  - 通过采用一个预先打好标的gold standard dataset ~~怕不是金标生抽哟~~ 来评估labelers. 当一个labeler能持续回答出与金标数据集相悖的答案时, 就可以认为这个labeler的答案不靠谱
  -  从多个labeler中获得全部data sample的回答, 然后实时或者后期vote. 这需要大多数的labeler都很靠谱

第一个方法可以如我们所愿的试试评估labelers, 但是需要预打标的数据集. 第二很清晰的评估种方法, 则是关注label的noise. 他并没有很明晰地去评估labeler.但他们都很ad hoc ~~大概这里可以认为是 专有的 ?~~, 缺乏一个将labelers的全局误差和expertise水平结合起来的方法.

本文在没有金标数据集的情况下, 提出了一个Bayesian模型, 用来学习基于multiple noisy labels的高斯过程分类器. 其次, 构建了an active classifier learning system which determines which users to label which unlabeled examples, 不仅可以选择sample, 更可以为其选择合适的labeler

---

# 8. Active Learning from Crowds

> The former is regarded as traditional active learning and the latter, which is essentially a kind of ensemble method [Zhou, 2012].(指的是多教师))

[Yan et al., 2011] Y. Yan, G. M. Fung, R. Rosales, and J. G.
Dy. Active learning from crowds. In ICML, pages 1161–
1168, 2011.

---

1. 去收集一个 golden ground-truth, 在一些问题中是很难甚至不可能的.
2. 一个annotator不一定能够有能力准确标注所有的data
3. 有些时候, 从许多不是那么专业的annotator中得出结果, 会比从一个专家得到结果更cheap
4. 合作和知识共享, 使得结合多annotator的技术会更必要 ~~这话好空~~


如果我们想要有效的标记(learn the most at a given cost), 传统的监督学习只需要找出那个最有用的data point. 但在如今的多labeler情景下, 就面临着一个问题: 多教师情况下, 这个'最'该怎么确立

本文关键就是我们是否能够为一个特定的task选择合适的labeler

优化分两步, 先选点, 再以此点去选labeler
1. Which new training point to pick?
    最简单的策略就是 uncertainty sampling, 选那些我们最不确定的. 在这个简单的二分类策略中, 选概率接近1/2的那些. 
2. Which expert to pick?
    按confidence去找.  optimization方面, 将原问题转化为一个bi-convex问题, 就能使用牛顿法去做优化了


# Get Another Label? Improving Data Quality and Data Mining Using Multiple, Noisy Labelers


>First, in their pioneering work, Sheng and Provost [Sheng
etal., 2008]assume that an annotator gives correct labels with
a certain probability, and the probability is assumed to be the
same for all annotators in all instances.

[Sheng et al., 2008] V. S. Sheng, F. Provost, and P. G. Ipeiro-
tis. Get another label? improving data quality and da-
ta mining using multiple, noisy labelers. In KDD, pages
614–622. ACM, 2008.

---

**这篇讲的是我之前很在意的一个点, 怎么从大量labeled但是labelers可能并不具有很强的expertise的data中得到结果. 就概念上来言很有种ensemble的感觉.**

**看到这里有个idea, 从ALCU想开, 要是一个没有很多expertise的labeler, 在它不熟悉的领域做出了确切回答, 是不是也可以有一个较高的概率相信他是正确的**

重复打标, 可以做到
1. 重复打标可以改善标签质量和模型质量, 但并不总是有效的
2. 当label是noisy的, 重复打标比单独打标更可取, 即使是在传统的labeling并不cheap的情形
3. 只要加工打标数据的cost不是free的, 即使为所有都多次打标这样简单的策略都可以得到可观的改善
4. 为一个小心选择的data重复打标通常来讲是更好的, we present a robust technique that combines different notions of uncertainty to select data points for which quality should be improved. 

This paper focuses on problems where it is possible to ob-
tain certain (noisy) data values (“labels”) relatively cheaply,
from multiple sources (“labelers”). 其实也还是多教师. 我们该怎么去利用这一票noisy, non-expert的label呢. 

面对noisy的标签时, 随着预处理的cost和打标的cost的比率的提示, 重复打标这个想法就很自然了: 为部分或者所有的data多次打标. 本文关注whether, when, which(data)这三点.
1. 逐渐减弱假设, 评估重复打标的影响on the quality of the resultant labels, 由labelers各自的confidence决定.
2. 忽略the cost of obtaining the unlabeled part of a data point, 重复打标也能够有几倍的改进

若每个labers都有自己的qualities, 那么, 是选择最好的那个, 还是用多个? 直观地想, 信息多应该会有个好结果. 事实上也是如此.

voting:
大多数vote方法都很直接, 但会导致一个缺点: 标签的不确定性丢失.
我们可以这样, assigns a weight 1/L, where L is the number of occurrences of this label in L.

# Efficiently learning the accuracy of labeling sources for selective sampling

>ThesecondtypeofALCmethods[Longetal., 2013][Don-
mez et al., 2009] [Zhao et al., 2014] does not assume all an-
notators will be have exactly the same. 

[Donmez et al., 2009] P. Donmez, J. G. Carbonell, and
J. Schneider. Efficiently learning the accuracy of labeling
sources for selective sampling. In KDD, pages 259–268.
ACM, 2009.

---

再许多的数据挖掘应用中, 为training set得到label是个很容易出错的过程, noisy的产生可能有很多原因, 比如主观想法的介入, 数据依赖, 不合适的特征等等. 在多教师情形下, 由于annotator的水平无法控制, class noise是一个常见的问题. 本文, 我们直面了这样的挑战, 以多个不确定其expertise的noisy labeler为基础去主动打标. 目标是估计每个laber的准确性然后使用这个估计去选择最好的labelers回答对应的提问. 但是, 要想算出来一个确切的reward function是不可能的因为真正的标签我们并不知道(看来这是没有金标数据集直接从labelers回答中总结答案). 我们假设这些labeler至少有0.5的正确率, 然后我们选择那些大多数的标签当作答案.

**idea, 实际上, 会不会有这种可能, 面对某个问题, 的确是会有大多数人都回答错这种'难题'存在, 能不能检测出来这种情况再交给expert去回答?**


# 11. An Active Learning Approach for Jointly Estimating Worker Performance and Annotation Reliability with Crowdsourced Data

>ThesecondtypeofALCmethods[Longetal., 2013][Don-
mez et al., 2009] [Zhao et al., 2014] does not assume all an-
notators will be have exactly the same. 

[Zhao et al., 2014] L. Zhao, Y. Zhang, and G. Sukthankar.
An active learning approach for jointly estimating worker
performance and annotation reliability with crowdsourced
data. arXiv preprint, 2014.

---

两点挑战: 有限的annotator人员, label noise. 即使每个annotator是非常cheap的, 但是总体cost会醉着请求标签量的提示会快速增长.

本文中, 使用了要给关于 **Labels, Abilities, and Difficulties** 的生成模型, 使用EM算法来学习参数.

![](http://otivusbsc.bkt.clouddn.com/3cf0ff46-669c-4913-b4fb-a33c46f52d50)


#  Efficiently learning the accuracy of labeling sources for selective sampling.

> In [Donmez et al., 2009] and [Long et al., 2013],
all instances are assumed to be of the same difficulty, and
different methods are proposed to estimate the reliability of
annotators.

[Donmez et al., 2009] P. Donmez, J. G. Carbonell, and
J. Schneider. Efficiently learning the accuracy of labeling
sources for selective sampling. In KDD, pages 259–268.
ACM, 2009.

---

主要在衡量labeler的可靠性. 
就是这篇文章提出了提出了IEThresh (Interval Estimate  ) 

---

# Active Learning from Multiple Knowledge Sources
