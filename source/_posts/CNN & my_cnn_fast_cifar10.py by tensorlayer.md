---
title: CNN & my_cnn_fast_cifar10.py by tensorlayer
date: 2017-07-30 17:04:10
tags:
---

## Convolutional Neural Network
CNN是一个很好的范例, 它告诉了我们, NN的架构可以由我们自己设计并且表现得很好

在你想用一个feature去比对图片的时候, 有三个重点:

 - pattern
 - location
 - subsampling

## 基本架构

image -> convolution -> pooling-> Flatten -> Fully Connected Feedforward Network -> softmax ->output

其中, 卷积层解决了pattern和location问题, 池化层解决了subsapling问题

<!-- more -->

### conv
假设一个6\*6\*1的image扔到这个CNN中, 第一个conv, 取一个3\*3的filter(kernel or neuron)与image中相同大小快依次做内积, 每次移动1个pix(stride=1), 于是得到了一个4\*4的image(不做padding), 假设我们有n个filter, 那么通过这个conv就得到了一个 4\*4\*n的Feature Map

如果是个RGB图像, 即6\*6\*3, 那么我们的filter也要是3\*3\*3的, 每个slice对应image的一个channel.

事实上,  我们仔细分析每个filter做了什么的话, 如果把一个filt看作一个neuron, filter内积的特性, 做到了两件事:

 - parameter sharing
 - drop connect
 
![ ](http://otivusbsc.bkt.clouddn.com/752e7d7d-6469-42b3-9e58-5d5ae31d005b)


顺带一说'卷积'这个概念. 很多人都说这个怎么就是卷积了, 不就是简单的一个inner production吗?
怎么看也不是那个积分u(t)h(a-t)形式. 其实, 要是从离散的角度去看卷积运算, 

>与其理解成翻转，不如理解成延迟后叠加。

这句话是我觉得说的非常好的一个揭示卷积本质的话. 再去看看filter的行为就很明了了.


### padding

根据tensorflow中的conv2d函数，我们先定义几个基本符号

1、输入矩阵 W×W，这里只考虑输入宽高相等的情况，如果不相等，推导方法一样，不多解释。

2、filter矩阵 F×F，卷积核

3、stride值 S，步长

4、输出宽高为 new_height、new_width

当然还有其他的一些具体的参数，这里就不再说明了。

我们知道，padding的方式在tensorflow里分两种，一种是VALID，一种是SAME，下面分别介绍这两种方式的实际操作方法。

1、如果padding = ‘VALID’

new_height = new_width = (W – F + 1) / S （结果向上取整）
也就是说，conv2d的VALID方式不会在原有输入的基础上添加新的像素（假定我们的输入是图片数据，因为只有图片才有像素），输出矩阵的大小直接按照公式计算即可。

2、如果padding = ‘SAME’

new_height = new_width = W / S （结果向上取整）

padding这段来自于

>作者：Traphix
链接：http://www.jianshu.com/p/05c4f1621c7e
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

### pooling
很多时候我们都是maxpooling, 其实averagepooling一样用的很多, 例如在cifar-10的cnn设计中, avgpooling被采用了在后两cnov层后
![ ](http://otivusbsc.bkt.clouddn.com/5a95606c-3f20-49c5-978f-cc23661c3764)

取kernel=(2,2)的maxpooling的话
6\*6 -> conv -> maxpooling -> 2\*2\*n

### flatten

直接把经过pooling得到的2\*2\*n拉直为一个向量, 每个slice头尾相接, 假设n=16, 这时我们就有了一个64dim的vector, 再扔进一个MLP, 输出一个10dim(假设作在mnist上), 最后接上一个softmax, 就得出分类结果


# my_cnn_fast_cifar10.py
下面是我按照pooling那里的cifar-10 fast model写的代码, backend是tensorflow, 框架tensorlayer.
```python
#! /usr/bin/python
# -*- coding: utf8 -*-

import tensorflow as tf
import tensorlayer as tl
import tensorlayer.layers as tll
import numpy as np
import time, os, io

sess = tf.InteractiveSession()

#train param
n_epoch = 500
learning_rate = 0.0001
print_freq = 1
batch_size = 128

#load cifar10
X_train, y_train, X_test, y_test = tl.files.load_cifar10_dataset(
                                    shape=(-1, 32, 32, 3), plotable=False)
#InitW(Gaussian with zero mean)
W_init1 = tf.truncated_normal_initializer(stddev=0.0001)
W_init2 = tf.truncated_normal_initializer(stddev=0.001)
W_init3 = W_init2
W_init4 = tf.truncated_normal_initializer(stddev=0.1)
W_init5 = W_init4
b_init4 = tf.truncated_normal_initializer(stddev=0.1)
b_init5 = b_init4

#placeholder
x = tf.placeholder(tf.float32, shape=[None, 32, 32, 3], name='x')
y_ = tf.placeholder(tf.int64, shape=[None, ], name='y_')

#model
nn = tll.InputLayer(x, name='Input1')
nn = tll.Conv2dLayer(nn, act=tf.nn.relu, shape=[5, 5, 3, 32], 
        strides=[1, 1, 1, 1], padding='SAME', W_init=W_init1, name='cnn1')
#output : (batchsize, 32, 32, 32)
nn = tll.PoolLayer(nn, ksize=[1, 3, 3, 1], strides=[1, 2, 2, 1], 
        padding='SAME', pool= tf.nn.max_pool, name='pool1_max')
#output : (batchsize, 16, 16, 32)
nn = tll.Conv2dLayer(nn, act=tf.nn.relu, shape=[5, 5, 32, 32], 
        strides=[1, 1, 1, 1], padding='SAME', W_init=W_init2, name='cnn2')
#output : (batchsize, 16, 16, 32)
nn = tll.PoolLayer(nn, ksize=[1, 3, 3, 1], strides=[1, 2, 2, 1], 
        padding='SAME', pool= tf.nn.avg_pool, name='pool2_avg')
#output : (batchsize, 8, 8, 32)
nn = tll.Conv2dLayer(nn, act=tf.nn.relu, shape=[5, 5, 32, 64], 
        strides=[1, 1, 1, 1], padding='SAME', W_init=W_init3, name='cnn3')
#output : (batchsize, 8, 8, 64)
nn = tll.PoolLayer(nn, ksize=[1, 3, 3, 1], strides=[1, 2, 2, 1], 
        padding='SAME', pool= tf.nn.avg_pool, name='pool3_avg')
#output : (batchsize, 4, 4, 64)
nn = tll.FlattenLayer(nn, name='Flattenlayer')
nn = tll.DenseLayer(nn, n_units=64, act=tf.nn.relu, 
        W_init=W_init4, b_init=b_init4, name='relu1')
nn = tll.DenseLayer(nn, n_units=10, act=tf.identity, 
        W_init=W_init5, b_init=b_init5, name='output1')
y = nn.outputs

#cost
ce = tl.cost.cross_entropy(y, y_, name='cost_cross_entropy')
L2 = 0
for p in tl.layers.get_variables_with_name('relu/W', train_only=True, printable=True):
    L2 += tf.contrib.layers.l2_regularizer(0.004)(p)
cost = ce + L2

#acc
correct_prediction = tf.equal(tf.argmax(y, 1), y_)
acc = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))

#train_op
train_params = nn.all_params
train_op = tf.train.AdamOptimizer(learning_rate, beta1=0.9, beta2=0.999,
    epsilon=1e-08, use_locking=False).minimize(cost, var_list=train_params)

tl.layers.initialize_global_variables(sess)

print('****')
nn.print_params(False)
print('****')
nn.print_layers()
print('****')

print('    learning_rate: %f' % learning_rate)
print('    batch_size: %d' % batch_size)
```
搭好了模型, 然后发现忘了写BN层了, 🤣明个再说
说个点
![ ](http://otivusbsc.bkt.clouddn.com/5a95606c-3f20-49c5-978f-cc23661c3764)
这里面, 卷积层参数, 给了kernel也就是filter的大小5/*5, 但没给strides, 却给的padding=2. 见过padding='SAME' or 'VAILD', 这个数字是个啥. 

研究了一下, 应该就是默认padding='SAME' , 然后看这个图
![](http://otivusbsc.bkt.clouddn.com/a56f0c24-ab26-406d-803f-1fbcea7377f9)

padding=2 就是最外层套2个pixel, 那么strides=1就很显然, 不然取2干什么.

---

走之前改了下初步, 加了个Bathchnormallayer

```python
#! /usr/bin/python
# -*- coding: utf8 -*-

import tensorflow as tf
import tensorlayer as tl
import tensorlayer.layers as tll
import numpy as np
import time, os, io

sess = tf.InteractiveSession()

#load cifar10
X_train, y_train, X_test, y_test = tl.files.load_cifar10_dataset(
                                    shape=(-1, 32, 32, 3), plotable=False)

def fast_model(x, y_, reuse, is_train):
    #InitW(Gaussian with zero mean)
    W_init1 = tf.truncated_normal_initializer(stddev=0.0001)
    W_init2 = tf.truncated_normal_initializer(stddev=0.001)
    W_init3 = W_init2
    W_init4 = tf.truncated_normal_initializer(stddev=0.1)
    W_init5 = W_init4
    b_init4 = tf.truncated_normal_initializer(stddev=0.1)
    b_init5 = b_init4
    
    with tf.variable_scope("model", reuse=reuse):
        tl.layers.set_name_reuse(reuse)
        #model
        nn = tll.InputLayer(x, name='Input1')
        nn = tll.Conv2dLayer(nn, act=tf.nn.relu, shape=[5, 5, 3, 32], 
                strides=[1, 1, 1, 1], padding='SAME', W_init=W_init1, name='cnn1')
        #output : (batchsize, 32, 32, 32)
        nn = tll.BatchNormLayer(nn, is_train, act=tf.nn.relu, name='batch1')
        nn = tll.PoolLayer(nn, ksize=[1, 3, 3, 1], strides=[1, 2, 2, 1], 
                padding='SAME', pool= tf.nn.max_pool, name='pool1_max')
        #output : (batchsize, 16, 16, 32)
        nn = tll.Conv2dLayer(nn, act=tf.nn.relu, shape=[5, 5, 32, 32], 
                strides=[1, 1, 1, 1], padding='SAME', W_init=W_init2, name='cnn2')
        #output : (batchsize, 16, 16, 32)
        nn = tll.BatchNormLayer(nn, is_train, act=tf.nn.relu, name='batch2')
        nn = tll.PoolLayer(nn, ksize=[1, 3, 3, 1], strides=[1, 2, 2, 1], 
                padding='SAME', pool= tf.nn.avg_pool, name='pool2_avg')
        #output : (batchsize, 8, 8, 32)
        nn = tll.Conv2dLayer(nn, act=tf.nn.relu, shape=[5, 5, 32, 64], 
                strides=[1, 1, 1, 1], padding='SAME', W_init=W_init3, name='cnn3')
        #output : (batchsize, 8, 8, 64)
        nn = tll.BatchNormLayer(nn, is_train, act=tf.nn.relu, name='batch3')
        nn = tll.PoolLayer(nn, ksize=[1, 3, 3, 1], strides=[1, 2, 2, 1], 
                padding='SAME', pool= tf.nn.avg_pool, name='pool3_avg')
        #output : (batchsize, 4, 4, 64)
        nn = tll.FlattenLayer(nn, name='Flattenlayer')
        nn = tll.DenseLayer(nn, n_units=64, act=tf.nn.relu, 
                W_init=W_init4, b_init=b_init4, name='relu1')
        nn = tll.DenseLayer(nn, n_units=10, act=tf.identity, 
                W_init=W_init5, b_init=b_init5, name='output1')
        y = nn.outputs

        #cost
        ce = tl.cost.cross_entropy(y, y_, name='cost_cross_entropy')
        L2 = 0
        for p in tl.layers.get_variables_with_name('relu/W', train_only=True, printable=True):
           L2 += tf.contrib.layers.l2_regularizer(0.004)(p)
        cost = ce + L2

        #acc
        correct_prediction = tf.equal(tf.argmax(y, 1), y_)
        acc = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
        
        return nn, cost, acc


#train param
n_epoch = 500
learning_rate = 0.0001
print_freq = 1
batch_size = 128

#placeholder
x = tf.placeholder(tf.float32, shape=[None, 32, 32, 3], name='x')
y_ = tf.placeholder(tf.int64, shape=[None, ], name='y_')

nn, cost, _ = fast_model(x, y_, False, is_train=True)
_, cost_test, acc = fast_model(x, y_, True, is_train=False)


#train_op
train_params = nn.all_params
train_op = tf.train.AdamOptimizer(learning_rate, beta1=0.9, beta2=0.999,
    epsilon=1e-08, use_locking=False).minimize(cost, var_list=train_params)

tl.layers.initialize_global_variables(sess)

print('****')
nn.print_params(False)
print('****')
nn.print_layers()
print('****')

print('    learning_rate: %f' % learning_rate)
print('    batch_size: %d' % batch_size)

'''
tl.utils.fit(sess, nn, train_op, cost, X_train, y_train, x, y_,
           acc=acc, batch_size=batch_size, n_epoch=n_epoch, print_freq=5,
           X_val=None, y_val=None, eval_train=False,
           tensorboard=True)

tl.utils.test(sess, nn, acc, X_test, y_test, x, y_, batch_size=None, cost=cost)

sess.close()
'''
```
num of params: 146090, 
参数数量比最原始版少了一个数量级. 很迷, 是不是写错了, 怎么少了这么多. 找个时间train下试试再说


