---
title: 从LeNet到DenseNet
date: 2017/11/30  12:58
tags: ML
---

# 网络模型

这篇是大致回顾一下一路走来的这些模型, 以后一些和具体模型联系比较紧密的东西也可以有地方放了.

## LeNet-5

LeNet-5是Yann LeCun在1998年设计的用于手写数字识别的卷积神经网络，是早期卷积神经网络中最有代表性的实验系统之一。

LeNet-5的这张图应该算是随处可见了.

![](http://otivusbsc.bkt.clouddn.com/77400155-7ecb-478c-ba50-07b92e2cfc71)

除了S2和C3的连接比较奇怪其他都是很容易理解的.

可以注意下的几点:

filter的大小是固定5\*5, pooling层是2\*2的maxpooling, 没有overlap, sigmoid的激活函数


<!-- more -->

## AlexNet

2012 年，Alex Krizhevsky、Ilya Sutskever 和 Geoffrey Hinton 创造了一个“大型的深度卷积神经网络”，也即现在众所周知的 AlexNet，赢得了当年的 ILSVRC冠军。

AlexNet:
 - 训练出当时最大规模的卷积神经网络
 - 实现高效的GPU卷积运算结构，也使得此后GPU成为深度学习的主要工具
 - 通过众多的skills（dropout、RELU、Data Augmentation）解决深层神经网络的过拟合问题，使得该网络在60 million参数的情况下依旧能很好收敛。这些方法现在已经成为了CNN不可或缺的一部分。

![](http://otivusbsc.bkt.clouddn.com/cc3429a9-0fea-4879-ac60-fd497319ed7a)

###  GPU

单个GTX 580 GPU只有3GB内存，这限制了可以在其上训练的网络的最大规模. 120万个训练样本才足以训练网络，这网络太大了，不适合在一个GPU上训练. --> 将网络分布在两个GPU上, 在每个GPU中放置一半核（或神经元）.

### Relu

传统的sigmoid和tanh是*饱和*的, 就是输入值达到一定大小之后就变化很小了. 而Relu则是\*非饱和\*的.

Sigmoid函数在正负饱和区的梯度都接近于0，所以会造成梯度弥散. 

relu函数在负半区的导数为0，一旦神经元激活值进入负半区，那么梯度就会为0，这个神经元不会经历训练，即所谓的稀疏性，起到了类似L1的正则化作用，可以在一定程度上缓解过拟合.

同时, Relu的导数非零即1易于计算(忽略0点的不可导性)

>当然，ReLU也是有缺点的，比如左边全部关了很容易导致某些隐藏节点永无翻身之日，所以后来又出现pReLU、random ReLU等改进，而且ReLU会很容易改变数据的分布，因此ReLU后加Batch Normalization也是常用的改进的方法。

### LRN层

局部响应归一化层（Local Response Normalization Layer）. 

基本被认为是没大用的东西. 大牛提出, 然后caffe历史遗留产物.

### Data augmentation

平移, 镜像, corp等等, 现在基本上用的也比较多了. 和改变RGB通道强度这种一起算作data预处理过程.

### Dropout

通常来说, Dropout都被认作是类似于bagging的一种方法. 


## VGG

ILSVRC 2014的第二名是Karen Simonyan和 Andrew Zisserman实现的卷积神经网络，现在称其为VGGNet。它主要的贡献是展示出网络的深度是算法优良性能的关键部分。

代表性的VGG-19
![](http://otivusbsc.bkt.clouddn.com/fe5442f0-c8cc-49bf-9f48-2e40ccef15f0)

VGG是在从Alex-net发展而来的网络。最关键的一点是使用小卷积核. 

>引入cs231n上面一段话：几个小滤波器卷积层的组合比一个大滤波器卷积层好：假设你一层一层地重叠了3个3x3的卷积层（层与层之间有非线性激活函数）。在这个排列下，第一个卷积层中的每个神经元都对输入数据体有一个3x3的视野。第二个卷积层上的神经元对第一个卷积层有一个3x3的视野，也就是对输入数据体有5x5的视野。同样，在第三个卷积层上的神经元对第二个卷积层有3x3的视野，也就是对输入数据体有7x7的视野。假设不采用这3个3x3的卷积层，二是使用一个单独的有7x7的感受野的卷积层，那么所有神经元的感受野也是7x7，但是就有一些缺点。首先，多个卷积层与非线性的激活层交替的结构，比单一卷积层的结构更能提取出深层的更好的特征。其次，假设所有的数据有C个通道，那么单独的7x7卷积层将会包含7*7*C=49C2个参数，而3个3x3的卷积层的组合仅有个3*（3*3*C）=27C2个参数。直观说来，最好选择带有小滤波器的卷积层组合，而不是用一个带有大的滤波器的卷积层。前者可以表达出输入数据中更多个强力特征，使用的参数也更少。唯一的不足是，在进行反向传播时，中间的卷积层可能会导致占用更多的内存。1*1 filter: 作用是在不影响输入输出维数的情况下，对输入线进行线性形变，然后通过Relu进行非线性处理，增加网络的非线性表达能力。


虽然VGG比Alex-net有更多的参数，更深的层次, 但是VGG只需要很少的迭代次数就开始收敛: 
 
 - 更深的深度和小的卷积核起到了隐式正则化作用。
 这点其实挺令人迷惑的, 人人都有自己的说法, 我来说说目前我自己的看法. 更深的深度, 一定程度下是可以提高整个网络的能力, 但没有经过特殊设计的网络结构加深反而会出问题, 前半句个人还是存疑的. 小的卷积核, 小的stride, 直观上来讲就是将一个大卷积核分散在了多个layer当中, 那相应的也经历了pooling和activate function, 就像是一个NN构成了这个大的卷积核, 里面小的核在投票.  

- 一些层的pre-initialisationpre-initialisation：网络A的权值W~（0,0.01）的高斯分布，bias为0；由于存在大量的ReLU函数，不好的权值初始值对于网络训练影响较大。为了绕开这个问题，作者现在通过随机的方式训练最浅的网络A；然后在训练其他网络时，把A的前4个卷基层（感觉是每个阶段的以第一卷积层）和最后全连接层的权值当做其他网络的初始值，未赋值的中间层通过随机初始化。


### GoogLeNet一家子---Inception v1

VGG给人带来了启发, 更深的网络可能就能够带来更好的表达能力. 但并不是说深就能深的, 网络的设计是至关重要的一环. 

 GoogLeNet Inception v1是GoogLeNet的最早版本，出现在2014年的《Going deeper with convolutions》。之所以名为“GoogLeNet”而非“GoogleNet”,文章说是为了向早期的LeNet致敬。而Inception这个名字更是令人浮想联翩, 第一瞬间就
 
![](http://otivusbsc.bkt.clouddn.com/62c63497-b64d-4d70-8660-6aa775c40382)

鬼谲的记忆植入, 一层一层的梦境就像是NN中一层一层的反馈. 

key: 稀疏连接:

 - 一方面现实生物神经系统的连接也是稀疏的，
 
 - 另一方面有文献表明：对于大规模稀疏的神经网络，可以通过分析激活值的统计特性和对高度相关的输出进行聚类来逐层构建出一个最优网络。这点表明臃肿的稀疏网络可能被不失性能地简化。

所以，现在的问题是有没有一种方法，既能保持网络结构的稀疏性，又能利用密集矩阵的高计算性能。大量的文献表明可以将稀疏矩阵聚类为较为密集的子矩阵来提高计算性能，据此论文提出了名为Inception 的结构来实现此目的。

![](http://otivusbsc.bkt.clouddn.com/064d0e96-0558-4dbd-8c52-5c1aa3d12fa6)

1 . 采用不同大小的卷积核意味着不同大小的感受野，最后拼接意味着不同尺度特征的融合； 

2 . 之所以卷积核大小采用1、3和5，主要是为了方便对齐。设定卷积步长stride=1之后，只要分别设定pad=0、1、2，那么卷积之后便可以得到相同维度的特征，然后这些特征就可以直接拼接在一起了； 

3 . 文章说很多地方都表明pooling挺有效，所以Inception里面也嵌入了。 

4 . 网络越到后面，特征越抽象，而且每个特征所涉及的感受野也更大了，因此随着层数的增加，3x3和5x5卷积的比例也要增加。

实际上卷积的使用就会带来稀疏性. 这里引人注目的是这个3\*3 的pooling, 看看resnet, 是不是有几分神似. 这样的一个pooling, 实际上可以看作上层的直接信息输入, 目前来看, 直接引入上层的信息会有相当好的效果. 一定程度上也减少了train过程中梯度消失和爆炸. 和后面具体结构图中看到的前两个softmax一个效果.

但是，使用5x5的卷积核仍然会带来巨大的计算量。 为此，文章借鉴'Network in network'，采用1x1卷积核来进行降维。 

例如：上一层的输出为100x100x128，经过具有256个输出的5x5卷积层之后(stride=1，pad=2)，输出数据为100x100x256。其中，卷积层的参数为128x5x5x256。假如上一层输出先经过具有32个输出的1x1卷积层，再经过具有256个输出的5x5卷积层，那么最终的输出数据仍为为100x100x256，但卷积参数量已经减少为128x1x1x32 + 32x5x5x256，大约减少了4倍。

改进为
![](http://otivusbsc.bkt.clouddn.com/f5007fdf-b9ce-4853-af93-2ffff67e2eda)

最后的GoogLeNet（Inception-V1）的结构图如下：

![](http://otivusbsc.bkt.clouddn.com/77c10c44-4922-42e0-94f2-5713527ba23f)

1 . 显然GoogLeNet采用了模块化的结构，方便增添和修改； 
2 . 网络最后采用了average pooling来代替全连接层，想法来自NIN,事实证明可以将TOP1 accuracy提高0.6%。但是，实际在最后还是加了一个全连接层，主要是为了方便以后大家finetune； 
3 . 虽然移除了全连接，但是网络中依然使用了Dropout ; 
4 . 为了避免梯度消失，网络额外增加了2个辅助的softmax用于向前传导梯度。文章中说这两个辅助的分类器的loss应该加一个衰减系数，但看caffe中的model也没有加任何衰减。此外，实际测试的时候，这两个额外的softmax会被去掉。

### GoogLeNet一家子---Inception v2v3

《Rethinking the Inception Architecture for Computer Vision》, V1的进化版. 

首先**通过实验** 给出了一些已经被证明有效的用于放大网络的通用准则和优化方法。这些准则和方法适用但不局限于Inception结构。

1 . 避免表达瓶颈，特别是在网络靠前的地方。 信息流前向传播过程中显然不能经过高度压缩的层，即表达瓶颈。从input到output，feature map的宽和高基本都会逐渐变小，但是不能一下子就变得很小。比如你上来就来个kernel = 7, stride = 5 ,这样显然不合适。 另外输出的维度channel，一般来说会逐渐增多(每层的num_output)，否则网络会很难训练。（特征维度并不代表信息的多少，只是作为一种估计的手段）

2 . 高维特征更易处理。 高维特征更易区分，会加快训练。

3. 可以在低维嵌入上进行空间汇聚而无需担心丢失很多信息。 比如在进行3x3卷积之前，可以对输入先进行降维而不会产生严重的后果。假设信息可以被简单压缩，那么训练就会加快。

4 . 平衡网络的宽度与深度。



再来说V2在哪里改进了.

首先就死, **用两个连续的3×3卷积层来代替5×5的kernel**. 带来的两个问题: 

1 . 这种替代会造成表达能力的下降吗？ 
后面有大量实验可以表明不会造成表达缺失；

2 . 3x3卷积之后还要再加激活吗？ 
作者也做了对比试验，表明添加非线性激活会提高性能。

从这里出发, 3×3还能怎么变得更小呢? 采用1×n卷积核

其次，**引入非对称卷积**。例如，将3x1的卷积和1x3的卷积串联起来，与直接进行3x3卷积的结果是等价的。这种卷积方式大大降低了参数量，从nxn降到了2xn，所以当n越大，降低得越多。实际上，作者发现在网络的前期使用这种分解效果并不好，还有在中度大小的feature map上使用效果才会更好。（对于mxm大小的feature map,建议m在12到20之间）.

![](http://otivusbsc.bkt.clouddn.com/621e0ea9-9ca8-4ab9-b6c4-be2f574f0dbb)


 - 图5是用3x3卷积序列来代替大卷积核；
 - 图6是用nx1卷积来代替大卷积核，这里设定n=7来应对17x17大小的feature map。该结构被正式用在GoogLeNet V2中。即非对称个卷积核，其实类似于卷积运算中，二维分解为1维计算，提高了计算速度。

第三种模式会在比较深的地方才采用.



>v3在raw的v2上做了如下变化：RMSProp替代SGD，在类别全连接层后加入 LSR层，将7x7卷积核由三个3x3卷积核替代。

BN方面会放在这里
https://lishiyuwhu.github.io/2017/11/22/Batch%20normalization/

LS:

深度学习用的labels一般都是one hot向量，用来指示classifier的唯一结果，这样的label像个脉冲函数, 有Dirac delta function的感觉

Labels的脉冲性质会引发两个不良后果：一是over-fitting，另外一个是降低了网络的适应性。

>First, it may result in over-fitting: if the model learns to assign full probability to the groundtruth label for each training example, it is not guaranteed to generalize. Second, it encourages the differences between the largest logit and all others to become large, and this, combined with the bounded gradient, reduces the ability of the model to adapt.

>Intuitively, this happens because the model becomes too confident about its predictions.

```
new_labels = (1.0 - label_smoothing) * one_hot_labels + label_smoothing / num_classes
```

### 1×1 kernel

1×1的kernel在之后的极深网络中都有重要的位置. 本质上来说, 就是把feature map按通道做叠加, 只不过写成kernel形式在现有网络结构中好表达而已, 接不接relu都是次要的. (注意, 1×1的kernel一个问题是并不会考虑相邻像素之间的联系. )(NIN原文中, 将其其看作一个跨通道的GAP)

作用:
 - 降维. 非常显著的降维效果
 bottleneck layer的具体效果计算:
 
![图片描述](http://otivusbsc.bkt.clouddn.com/1ec90333-c77b-46c6-9be8-cb7c5ece5849)
![图片描述](http://otivusbsc.bkt.clouddn.com/64913b4f-4cf5-4e05-9a12-d69e70923543)

 - 后面如果加上relu这种激活函数, 同时也引入了非线性.
 
 

## ResNet
![图片描述](http://otivusbsc.bkt.clouddn.com/724a0ff8-6039-4f0c-86a3-be7d648022bb)
![](http://otivusbsc.bkt.clouddn.com/75360de3-9e42-4dee-afa4-6f1b3241bae1)

分析:

[[1605.06431] Residual Networks Behave Like Ensembles of Relatively Shallow Networks](https://arxiv.org/abs/1605.06431)

主要思想就是, ResNet其实是一个ensemble系统.

![](http://otivusbsc.bkt.clouddn.com/917e25dc-551a-45f8-a524-296e7a03e8f2)

文章中通过分析梯度, 发现ResNet其实大部分都是中等深度的网络.

所以说, resnet真的解决了梯度消失问题吗? 并没有, 那些少数的最深的网络随着BP梯度依然越来越小, 只不过的skip connect把梯度直接传回去, 让总体网络梯度消失不明显了.

而Szegedy在V4版本文章中, 表示: Residual Connections加速了网络收敛，真正提高网络精度的是更大的网络规模

>Although the residual version converges faster, the final accuracy seems to mainly depend on the model size.

## DenseNet

[Densely Connected Convolutional Networks](https://arxiv.org/abs/1608.06993)


>Prior work has shown that there is great redundancy within the feature maps of the individual layers in ResNets. In DenseNets, all layers have direct access to every feature map from all preceding layers, which means that there is no need to re-learn redundant feature maps. Consequently, DenseNet layers are very narrow (on the order of 12 feature maps per layer) and only add a small set of feature maps to the “collective knowledge” of the whole network.


DenseNet的关键词: **Feature Reuse** . 而正是高度的重用, 可以让其把网络设计的特别的窄, 每层只有少量特征图的学习, 降低冗余. 

具体结构:

![图片描述](http://otivusbsc.bkt.clouddn.com/85ccfebe-195b-423f-92ee-0e8afe3503e1)

![图片描述](http://otivusbsc.bkt.clouddn.com/9816124b-4741-4391-b1e3-3f4b68ebcca5)


其实无论densenet还是resnet, 都是highway net的那个skip connection策略:
对于某些的输入不加选择的让其进入之后的layer(skip)，从而实现信息流的整合，避免了信息在层间传递的丢失和梯度消失的问题(还抑制了某些噪声的产生).

DenseNet的优点是参数少, 计算速度快, 高度的特征重用还带来了一定的抗过拟合能力(当然也可能只是参数减少带来的).  每一层都直接的与loss和input相连, 极大的减轻了梯度消失问题, 使得densenet在极深情形下依然保持良好的性能.

(内存使用就是另一回事了, 专门有篇优化内存使用的文章[Memory-Efficient Implementation of DenseNets](https://arxiv.org/pdf/1707.06990.pdf))

作者黄高博士及刘壮在访谈中也提到:

>DenseNet 的想法很大程度上源于我们去年发表在 ECCV 上的一个叫做随机深度网络（Deep networks with stochastic depth）工作。当时我们提出了一种类似于 Dropout 的方法来改进ResNet。我们发现在训练过程中的每一步都随机地「扔掉」（drop）一些层，可以显著的提高 ResNet 的泛化性能。这个方法的成功至少带给我们两点启发：

>首先，它说明了神经网络其实并不一定要是一个递进层级结构，也就是说网络中的某一层可以不仅仅依赖于紧邻的上一层的特征，而可以依赖于更前面层学习的特征。想像一下在随机深度网络中，当第 l 层被扔掉之后，第 l+1 层就被直接连到了第 l-1 层；当第 2 到了第 l 层都被扔掉之后，第 l+1 层就直接用到了第 1 层的特征。因此，随机深度网络其实可以看成一个具有随机密集连接的 DenseNet。

>其次，我们在训练的过程中随机扔掉很多层也不会破坏算法的收敛，说明了 ResNet 具有比较明显的冗余性，网络中的每一层都只提取了很少的特征（即所谓的残差）。实际上，我们将训练好的 ResNet 随机的去掉几层，对网络的预测结果也不会产生太大的影响。既然每一层学习的特征这么少，能不能降低它的计算量来减小冗余呢？

>DenseNet 的设计正是基于以上两点观察。我们让网络中的每一层都直接与其前面层相连，实现特征的重复利用；同时把网络的每一层设计得特别「窄」，即只学习非常少的特征图（最极端情况就是每一层只学习一个特征图），达到降低冗余性的目的。这两点也是 DenseNet 与其他网络最主要的不同。需要强调的是，第一点是第二点的前提，没有密集连接，我们是不可能把网络设计得太窄的，否则训练会出现欠拟合（under-fitting）现象，即使 ResNet 也是如此。

-------

# 模块认知

接下来, 就对原始CNN的四个模块来分别说说.

## Kernel

早期的kernel都设计的比较大, 7×7这种, 当时的观点是需要捕捉邻域信息(当然CNN也是为了处理这种相关性较强的信息). 而后发现, 连续的小的kernel一样能有大卷积核的高维抽象的效果, 并且做重要的是, 小卷积核在保持模型的capacity同时, 还有少得多的参数, 这对计算以及抗过拟合都起到了很大的帮助. 后来大量使用3×3的kernel, 乃至1×3 3×1这种非对称卷积(需要小心使用). 而1×1kernel更多是起到了降维的作用.

## Activate function

Sigmoid函数$Sigmoid(x) = \frac{1}{1+e^(-x)}$, tanh函数$tanh(x) = 2Sigmoid(2x) - 1$

原始的sigmoid/tanh有一个非常致命的缺点，他们是饱和(saturate)函数. 当输入非常大或者非常小的时候，这些神经元的梯度是接近于0的. 如果你的初始值很大的话，大部分神经元可能都会处在饱和的状态而把gradient kill掉，这会导致网络变的很难学习。
使用 ReLU得到的SGD的收敛速度会比sigmoid/tanh快很多，相比于sigmoid/tanh，ReLU只需要一个阈值就可以得到激活值，而不用去算一大堆复杂的运算。Relu能加速训练，导数好求，在反向传播时速度快，激活部分梯度是1，梯度不容易消失，而Sigmoid和tanh在两端梯度消失严重。

Relu的缺陷:
 - 在x<0时, 输出y<0, 同时导数也等于0, 导致参数不会再更新, 这种情况被称为dying relu.(当然这个是好是坏另还是有争议, 有人认为这是一种有选择的drop out)
 - 当我们的输入时一般会经过处理让其正规化, 但经过relu单元过后就不可避免的产生了bias.(BN层可以很好的解决这个问题)


![图片描述](http://otivusbsc.bkt.clouddn.com/5d38c608-b1cc-4325-b2c1-fb97eb419f65)

https://www.zhihu.com/question/29021768

其实激活函数这方面还是一团迷雾, CNN里面大家都说饱和的激活函数不好, 看一眼隔壁RNN tanh反而是第一选择, 这又怎么说得清.

## Pooling

pooling层: 
 - 引入非线性.
 - subsampling
 
 subsampling首先是引入了一定的平移不变性, 其次是以一定信息损失的代价减少了参数, 减少了计算量, 也是计算量和性能的trade-off.
 
stride=1的pooling layer, 主要是增大了感受野, 所以也都是meanpool
strde>1 的pooling layer, 就是降维减少计算量
 
 就这几个新的模型来看, 似乎avgpool也并不是那么的不堪, 在网络的后段, 高维特征部分, 使用avgpool减少信息的损失似乎是个可选项. 
 比如说这个快速cifar-10结构
 
 ![](http://otivusbsc.bkt.clouddn.com/b2a55e7e-918b-4ae1-b98c-f0ff2ca27c88) http://www.cnblogs.com/neopenx/p/4480701.html

## Full Connected Layer

关于全连接层的设计理念. 源于早期CV中的工作方法, 手工设计特征提取算子用于提取特征, 再用adaboost svm去作分类.

到了NN结构的设计中, 依然有一定的这种想法存在: conv+relu+pooling堆叠作特征提取, 之后一个flatten层作为最后全连接层输入进行分类. 虽然整个CNN一个end-to-end的模式, 但内在的特征提取和分类过程依然是分离的. 

这当然是很自然的想法. 但是之后通过分析发现, FC层在整个网络的参数中占了相当大一部分的比例(80%), 于是NIN就出现了GAP(global average pooling)来代替FC层的做法, 最后的效果发现模型准确性并没有降低, 同时参数量也下降了不少. 直接在卷积堆叠最后拉出来10个feature map(假设10类), 每个feature map作GAP得到的值当作这个类的confidence value, 再扔进softmax中分类.




---
#参考:

http://blog.csdn.net/KangRoger/article/details/69218625
https://zhuanlan.zhihu.com/p/31006686
http://blog.csdn.net/shuzfan/article/details/50738394
http://kaiminghe.com/ilsvrc15/ilsvrc2015_deep_residual_learning_kaiminghe.pdf
http://www.jianshu.com/p/0cc42b8e6d25
http://www.sohu.com/a/161923204_651893
http://www.caffecn.cn/?/question/136