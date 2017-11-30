---
title: TFrecords使用, 数据集制作相关总结
date: 2017/11/30  9:15
tags: ML
---

TFRecords其实是一种二进制文件，虽然它不如其他格式好理解，但是它能更好的利用内存，更方便复制和移动，并且不需要单独的标签文件（等会儿就知道为什么了）… …总而言之，这样的文件格式好处多多，所以让我们用起来吧。

TFRecords文件包含了tf.train.Example 协议内存块(protocol buffer)(协议内存块包含了字段 Features)。我们可以写一段代码获取你的数据， 将数据填入到Example协议内存块(protocol buffer)，将协议内存块序列化为一个字符串， 并且通过tf.python_io.TFRecordWriter 写入到TFRecords文件。

从TFRecords文件中读取数据， 可以使用tf.TFRecordReader的tf.parse_single_example解析器。这个操作可以将Example协议内存块(protocol buffer)解析为张量。

以上是复制的.


<!-- more -->




## 参考文章

http://blog.csdn.net/u012759136/article/details/52232266

http://blog.csdn.net/tengxing007/article/details/56847828

http://blog.csdn.net/tengxing007/article/details/54428262

http://www.shellsec.com/news/33788.html

https://github.com/ycszen/TensorFlowLaboratory/tree/master/mcnn