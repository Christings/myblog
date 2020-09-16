---
title: TensorFlow-5.矩阵
date: 2020-02-18 23:19:19
tags: TensorFlow
---
代码：4-matrix.ipynb

矩阵运算，例如执行乘法、加法和减法，是任何神经网络中信号传播的重要操作。通常在计算中需要随机矩阵、零矩阵、一矩阵或者单位矩阵。

所有加法、减、除、乘（按元素相乘）、取余等矩阵的算术运算都要求两个张量矩阵是相同的数据类型，否则就会产生错误。

可以使用 tf.cast() 将张量从一种数据类型转换为另一种数据类型。

如果在整数张量之间进行除法，最好使用 tf.truediv(a，b)，因为它首先将整数张量转换为浮点类，然后再执行按位相除。

```
import tensorflow as tf

m1=tf.eye(5)
m2=tf.Variable(tf.eye(10))
m3=tf.Variable(tf.random.normal([5,10]))
m4=tf.matmul(m3,m2)
m5=tf.Variable(tf.random.uniform([5,10],0,2,dtype=tf.int32))
m6=tf.cast(m5,dtype=tf.float32)
m7=tf.add(m1,tf.ones(5))
m8=m1-tf.ones(5)
```
