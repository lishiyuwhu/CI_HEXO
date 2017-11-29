---
title: tensorlayer的坑.md
date: 2017/11/28  9:29
tags: ML
---

## tl.cost.cross_entropy

默认的tl.cost.cross_entropy是sparse_softmax, 即是对于label是[2,4,1]这种, 而非[[0,1,0,0],[0,0,0,1],[1,0,0,0]]这种形式


```python
    cost = tl.cost.cross_entropy(y, y_, name='xentropy')

    # tl.cost.cross_entropy is tf.nn.sparse_softmax_cross_entropy_with_logits
    #cost = tf.reduce_mean(tf.nn.sparse_softmax_cross_entropy_with_logits(y, y_,name='xentropy'))
```

<!-- more -->

## all_layers in TF-Slim

The due to TF-Slim stores the layers as dictionary, the ``all_layers`` in this network ** is not in order ! ** Fortunately, the ``all_params`` are in order.