---
title: TFrecords使用, 数据集制作相关总结
date: 2017/11/30  9:15
tags: ML
---

>在TensorFlow中以輸入管線的方式從檔案讀取資料時，流程通常包含以下幾個步驟：
 - 提供檔案名稱。
 - 打散檔案名稱。（可省略）
 - 設定 epoch 限制。（可省略）
 - 建立檔案名稱佇列（filename queue）。
 - 根據檔案格式，建立檔案讀取器。
 - 根據資料格式，建立資料解析器。
 - 資料前處理。（可省略）
 - 建立資料佇列。

# 先期准备和转换TFrecords格式

<!-- more -->

```python
#! /usr/bin/python3
# -*- coding: utf-8 -*-
# @Time    : 2017/12/12 20:08
# @Author  : Shiyu Li
# @Software: PyCharm
#
import tensorflow as tf
import os
from PIL import Image
import random
import shutil

def makefile(root, data, typename, NUM):
    '''
    :param root: target file of '/train' and '/test'
    :param data: the data file you want to corp
    :param typename: file name in '/train' and '/test'
    :param NUM: the num of data in '/train'
    :return: None
    '''

    try:
        os.makedirs(root + '/train')
        os.makedirs(root + '/test')

    except BaseException:
        print('=================================================')
        print("There exist '/train' '/test', delete first please.")

    try:
        os.makedirs(root + '/train/' + typename)
        os.makedirs(root + '/test/' + typename)
    except BaseException:
        print("There exist '/%s', delete first please." % typename)


    name_list = os.listdir(data)
    random.shuffle(name_list)

    count = 0
    for name in name_list:
        if count < NUM:
            print('train data :%s' % name)
            shutil.copyfile(os.path.join(data, name), os.path.join(root + '/train/' + typename + '/', typename + name))
            count += 1
        else:
            print('test data :%s' % name)
            shutil.copyfile(os.path.join(data, name), os.path.join(root + '/test/'+ typename + '/', typename + name))

def encode(filename):
    '''
    file structure
    -img-to-TR.py
    -train
      -cover
        -1.pgm
        -3.pgm
      -stego
        -2.pgm
        -6.pgm
    -test
      -cover
        -5.pgm
        -8.pgm
      -stego
        -213.pgm
        -223.pgm

    :param filename:
    :return:
    '''
    cwd = os.getcwd()
    root = cwd + '\\' + filename + '\\'
    name = filename + 'BossBase-1.01-hugo-alpha=0.4.tfrecords'
    print(name)
    writer = tf.python_io.TFRecordWriter(name)
    classes = {'cover', 'stego'}

    #for image_filename, label in zip(image_filename_list, label_list):

    for index, name in enumerate(classes):
        class_path = root + name + '\\'
        for img_name in os.listdir(class_path):
            img_path = class_path + img_name
            img = Image.open(img_path)
            print(img_name)

            #data = np.array(img.getdata())
            # 可视化 Image.fromarray(data.reshape([512,512])).show()

            data = img.tobytes()
            # 可视化 Image.frombytes('L', [512,512], data).show()
            example = tf.train.Example(features=tf.train.Features(feature={
                "label": tf.train.Feature(int64_list=tf.train.Int64List(value=[index])),
                'img_raw': tf.train.Feature(bytes_list=tf.train.BytesList(value=[data]))
            }))
            writer.write(example.SerializeToString())
    writer.close()

if __name__ == '__main__':

    # cover_data = 'BossBase-1.01-cover'
    # stego_data = 'BossBase-1.01-hugo-alpha=0.4'
    # root = os.getcwd()
    # makefile(root, cover_data, typename='cover', NUM=8000)
    # makefile(root, stego_data, typename='stego', NUM=8000)

    encode('train')
    encode('test')
```

# 读取和TFrecords中图片的解码可视化

```python
#! /usr/bin/python3
# -*- coding: utf-8 -*-
# @Time    : 2017/12/12 20:08
# @Author  : Shiyu Li
# @Software: PyCharm

import tensorflow as tf
import numpy as np
from PIL import Image


def read_and_decode(filename):
    """ Return tensor to read from TFRecord """
    filename_queue = tf.train.string_input_producer([filename])
    reader = tf.TFRecordReader()
    _, serialized_example = reader.read(filename_queue)
    features = tf.parse_single_example(serialized_example,
                                       features={
                                           'label': tf.FixedLenFeature([], tf.int64),
                                           'img_raw' : tf.FixedLenFeature([], tf.string),
                                       })
    img = tf.decode_raw(features['img_raw'], tf.uint8)
    img = tf.reshape(img, [512, 512, 1])
    # img = tf.cast(img, tf.float32) # if you want to use tfrecords as input.
    label = tf.cast(features['label'], tf.int32)
    return img, label

# visualize data
img, label = read_and_decode("testBossBase-1.01-hugo-alpha=0.4.tfrecords")
img_batch, label_batch = tf.train.shuffle_batch([img, label],
                                                batch_size=4,
                                                capacity=50000,
                                                min_after_dequeue=10000,
                                                num_threads=1)
print("img_batch   : %s" % img_batch._shape)
print("label_batch : %s" % label_batch._shape)

init = tf.global_variables_initializer()
with tf.Session() as sess:
    sess.run(init)
    coord = tf.train.Coordinator()
    threads = tf.train.start_queue_runners(sess=sess, coord=coord)

    val, l = sess.run([img_batch, label_batch])
    print(type(val))
    show_img = Image.fromarray(np.squeeze(val[0], axis=(2,)))
    show_img.show()

    coord.request_stop()
    coord.join(threads)
    sess.close()
```




## 参考文章

http://ycszen.github.io/2016/08/17/TensorFlow%E9%AB%98%E6%95%88%E8%AF%BB%E5%8F%96%E6%95%B0%E6%8D%AE/

http://blog.csdn.net/u012759136/article/details/52232266

http://blog.csdn.net/tengxing007/article/details/56847828

http://blog.csdn.net/tengxing007/article/details/54428262

http://www.shellsec.com/news/33788.html

https://github.com/ycszen/TensorFlowLaboratory/tree/master/mcnn

https://blog.gtwang.org/programming/tensorflow-input-pipeline-notes/