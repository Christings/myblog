---
title: TensorFlow-7-conv2d的padding及参数解释
date: 2020-04-17 15:51:56
tags: TensorFlow
categories:
        - 机器学习和深度学习
        - TensorFlow
---
### 1、padding的方式：
![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/padding.jpeg](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/padding.jpeg)

说明：

1、摘录自http://stackoverflow.com/questions/37674306/what-is-the-difference-between-same-and-valid-padding-in-tf-nn-max-pool-of-t

2、不同的padding方式,VALID是采用丢弃的方式,比如上述的input_width=13,只允许滑动2次,多余的元素全部丢掉

3、SAME的方式,采用的是补全的方式,对于上述的情况,允许滑动3次,但是需要补3个元素,左奇右偶,在左边补一个0,右边补2个0

4、For the SAME padding, the output height and width are computed as:


    out_height = ceil(float(in_height) / float(strides[1]))

    out_width = ceil(float(in_width) / float(strides[2]))

    For the VALID padding, the output height and width are computed as:

    out_height = ceil(float(in_height - filter_height + 1) / float(strides[1]))

    out_width = ceil(float(in_width - filter_width + 1) / float(strides[2]))

### 2、conv2d的参数：

 

1、strides[0] = strides[3] = 1

### 3、conv2d的参数解释：

tf.nn.conv2d(input, filter, strides, padding, use_cudnn_on_gpu=None, name=None)
除去name参数用以指定该操作的name，与方法有关的一共五个参数：


第一个参数input：指需要做卷积的输入图像，它要求是一个Tensor，具有[batch, in_height, in_width, in_channels]这样的shape，具体含义是[训练时一个batch的图片数量, 图片高度, 图片宽度, 图像通道数]，注意这是一个4维的Tensor，要求类型为float32和float64其中之一

第二个参数filter：相当于CNN中的卷积核，它要求是一个Tensor，具有[filter_height, filter_width, in_channels, out_channels]这样的shape，具体含义是[卷积核的高度，卷积核的宽度，图像通道数，卷积核个数]，要求类型与参数input相同,filter的通道数要求与input的in_channels一致，有一个地方需要注意，第三维in_channels，就是参数input的第四维

第三个参数strides：卷积时在图像每一维的步长，这是一个一维的向量，长度4，strides[0]=strides[3]=1

第四个参数padding：string类型的量，只能是"SAME","VALID"其中之一，这个值决定了不同的卷积方式（后面会介绍）

第五个参数：use_cudnn_on_gpu:bool类型，是否使用cudnn加速，默认为true

结果返回一个Tensor，这个输出，就是我们常说的feature map

### 4、conv2d的例子：

那么TensorFlow的卷积具体是怎样实现的呢，用一些例子去解释它：

import tensorflow as tf
#case 2
input = tf.Variable(tf.random_normal([1,3,3,5]))
filter = tf.Variable(tf.random_normal([1,1,5,1]))
op = tf.nn.conv2d(input, filter, strides=[1, 1, 1, 1], padding='VALID')

with tf.Session() as sess:
sess.run(tf.initialize_all_variables())
res = (sess.run(op))
print (res.shape)

import tensorflow as tf

input = tf.Variable(tf.random_normal([1,5,5,5]))
filter = tf.Variable(tf.random_normal([3,3,5,1]))
op = tf.nn.conv2d(input, filter, strides=[1, 1, 1, 1], padding='VALID')

with tf.Session() as sess:
sess.run(tf.initialize_all_variables())
res = (sess.run(op))
print (res.shape)


说明：


1、使用VALID方式,feature map的尺寸为
out_height = ceil(float(in_height - filter_height + 1) / float(strides[1]))=(5-3+1)/1 = 3

out_width = ceil(float(in_width - filter_width + 1) / float(strides[2])) = (5-3+1)/1 = 3

所以,feature map的尺寸为3*3

2、filter的参数个数为3*3*5*1,也即对于输入的每个通道数都对应于一个3*3的滤波器,然后共5个通道数,conv2d的过程就是对5个输入进行点击然后求和,得到一张feature map。如果要得到3张feature map,那么应该使用的参数为3*3*5*3个参数.
