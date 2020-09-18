---
title: TensorFlow-4.常量、变量和占位符
date: 2020-02-18 21:42:33
tags: TensorFlow
category:
        - 机器学习和深度学习
        - TensorFlow
---

文档：https://www.tensorflow.org/api_docs/python/
代码：3-var.ipynb

张量，可理解为一个 n 维矩阵，所有类型的数据，包括标量、矢量和矩阵等都是特殊类型的张量。

TensorFlow 支持以下三种类型的张量：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/tensor.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/tensor.png)

1、常量：常量是其值不能改变的张量。

2、变量：当一个量在会话中的值需要更新时，使用变量来表示。例如，在神经网络中，权重需要在训练期间更新，可以通过将权重声明为变量来实现。变量在使用前需要被显示初始化。另外需要注意的是，常量存储在计算图的定义中，每次加载图时都会加载相关变量。换句话说，它们是占用内存的。另一方面，变量又是分开存储的。它们可以存储在参数服务器上。

3、占位符：用于将值输入 TensorFlow 图中。它们可以和 feed_dict 一起使用来输入数据。在训练神经网络时，它们通常用于提供新的训练样本。在会话中运行计算图时，可以为占位符赋值。这样在构建一个计算图时不需要真正地输入数据。需要注意的是，占位符不包含任何数据，因此不需要初始化它们。

## 一、常量
```
import tensorflow as tf

t1=tf.constant(4)
t2=tf.constant([1,2,3])

# tf.zeros([M,N],tf.dtype)
# 创建一个形如 [M，N] 的零元素矩阵，数据类型（dtype）可以是 int32、float32 等
t3=tf.zeros([2,3],tf.int32)

t4=tf.zeros_like(t2)
t5=tf.ones_like(t2)

# 创建一个所有元素都设为 1 的张量
# tf.ones([M,N],tf.dtype)
t6=tf.ones([2,3],tf.int32)

# 在一定范围内生成一个从初值到终值等差排布的序列：tf.linspace(start,stop,num)
# 相应的值为 (stop-start)/(num-1)
t7=tf.linspace(2.0,5.0,5)

# 从开始（默认值=0）生成一个数字序列，增量为 delta（默认值=1），直到终值（但不包括终值）：tf.range(start,limit,delta)
t8=tf.range(10)



# TensorFlow 允许创建具有不同分布的随机张量：
# 1.使用以下语句创建一个具有一定均值（默认值=0.0）和标准差（默认值=1.0）、形状为 [M，N] 的正态分布随机数组：
# t9=tf.random_normal([2,3],mead=2.0,stddev=4.0,seed=12)
t9=tf.random.normal([2,3],mean=2.0,stddev=4.0,seed=12) # 2.0语法

# 参数:
# shape: 一维的张量，也是输出的张量。
# mean: 正态分布的均值。
# stddev: 正态分布的标准差。
# dtype: 输出的类型。
# seed: 一个整数，当设置之后，每次生成的随机数都一样。
# name: 操作的名字。
# tf.random.normal(shape, mean=0.0, stddev=1.0, dtype=tf.float32, seed=None, name=None)

# 2.创建一个具有一定均值（默认值=0.0）和标准差（默认值=1.0）、形状为 [M，N] 的截尾正态分布随机数组:
t10=tf.random.truncated_normal([1,5],stddev=2,seed=12)

# 3.要在种子的 [minval（default=0），maxval] 范围内创建形状为 [M，N] 的给定伽马分布随机数组:
t11=tf.random.uniform([2,3],maxval=4,seed=12)

# 4.要将给定的张量随机裁剪为指定的大小:
# t12=tf.random_crop(t11,[2,5],seed=12) # ??? 随机从张量 t11 中裁剪出一个大小为 [2，5] 的张量
t13=tf.random.shuffle(t11) # 以随机的顺序来呈现训练样本


# 5.随机生成的张量受初始种子值的影响。要在多次运行或会话中获得相同的随机数，应该将种子设置为一个常数值。当使用大量的随机张量时，可以使用 tf.set_random_seed() 来为所有随机产生的张量设置种子
t14=tf.compat.v1.set_random_seed(54) # 种子只能有整数值。
```

```
tf.Tensor(4, shape=(), dtype=int32)
tf.Tensor([1 2 3], shape=(3,), dtype=int32)
tf.Tensor(
[[0 0 0]
 [0 0 0]], shape=(2, 3), dtype=int32)
tf.Tensor([0 0 0], shape=(3,), dtype=int32)
tf.Tensor([1 1 1], shape=(3,), dtype=int32)
tf.Tensor(
[[1 1 1]
 [1 1 1]], shape=(2, 3), dtype=int32)
tf.Tensor([2.   2.75 3.5  4.25 5.  ], shape=(5,), dtype=float32)
tf.Tensor([0 1 2 3 4 5 6 7 8 9], shape=(10,), dtype=int32)
tf.Tensor(
[[-2.2231169 -0.7829838 -2.3765397]
 [-2.0533948  5.207492   4.025979 ]], shape=(2, 3), dtype=float32)
tf.Tensor([[-1.9783968   0.05632517 -3.7346437  -0.21532871 -1.6093115 ]], shape=(1, 5), dtype=float32)
tf.Tensor(
[[0.94491434 2.346364   0.2467246 ]
 [2.254159   3.8856897  0.30119896]], shape=(2, 3), dtype=float32)
tf.Tensor(
[[2.254159   3.8856897  0.30119896]
 [0.94491434 2.346364   0.2467246 ]], shape=(2, 3), dtype=float32)
None
```

## 二、变量
    通过使用变量类来创建。变量的定义还包括应该初始化的常量/随机值。

```
ta=tf.random.uniform([50,50],0,10,seed=0)
tb=tf.Variable(ta)
tc=tf.Variable(ta)

# 注意：变量通常在神经网络中表示权重和偏置。
# 权重变量使用正态分布随机初始化，均值为 0，标准差为 2，权重大小为 100×100。偏置由 100 个元素组成，每个元素初始化为 0。
weights=tf.Variable(tf.random.normal([100,100],stddev=2))
bias=tf.Variable(tf.zeros([100]),name='biases')

# 指定一个变量来初始化另一个变量
weight2=tf.Variable(weights.initialized_value(),name='w2')

# 变量的定义将指定变量如何被初始化，但是必须显式初始化所有的声明变量。
inital_op=tf.compat.v1.global_variables_initializer()

# 每个变量也可以在运行图中单独使用 tf.Variable.initializer 来初始化
bias.initializer

# 保存变量：使用 Saver 类来保存变量，定义一个 Saver 操作对象
# saver=tf.compat.v1.train.Saver() ???
```

## 三、占位符

    作用：用于将数据提供给计算图。
    tf.placeholder(dtype,shape=None,name=None)
    dtype 定占位符的数据类型，并且必须在声明占位符时指定。

注意，与 Python/Numpy 序列不同，TensorFlow 序列不可迭代。
for i in tf.range(10) # error


## 四、追加

## TensorFlow基础架构
### Session--会话控制
Session 是 TensorFlow 为了控制,和输出文件的执行的语句.  
运行 session.run() 可以获得你的运算结果, 或者是你所要运算的部分.

    `# method 1
    sess = tf.Session()
    result = sess.run(product)
    print(result)
    sess.close()
    
    # method 2
    with tf.Session() as sess:
        result2 = sess.run(product)
        print(result2)`
### Variable--变量
在 Tensorflow 中，定义了某字符串是变量，它才是变量，这一点是与 Python 所不同的。

定义语法： state = tf.Variable()

    `import tensorflow as tf

    state = tf.Variable(0, name='counter')
    
    # 定义常量 one
    one = tf.constant(1)
    
    # 定义加法步骤 (注: 此步并没有直接计算)
    new_value = tf.add(state, one)
    
    # 将 State 更新成 new_value
    update = tf.assign(state, new_value)
    # 如果定义 Variable, 就一定要 initialize
    init = tf.global_variables_initializer()
     
    # 使用 Session
    with tf.Session() as sess:
        sess.run(init)
        for _ in range(3):
            sess.run(update)
            print(sess.run(state))`
注意：直接 print(state) 不起作用！！

一定要把 sess 的指针指向 state 再进行 print 才能得到想要的结果！
### Placeholder--传入值
placeholder 是 Tensorflow 中的占位符，暂时储存变量.

Tensorflow 如果想要从外部传入data, 那就需要用到 tf.placeholder(), 然后以这种形式传输数据 sess.run(***, feed_dict={input: **}).

    `import tensorflow as tf

    #在 Tensorflow 中需要定义 placeholder 的 type ，一般为 float32 形式
    input1 = tf.placeholder(tf.float32)
    input2 = tf.placeholder(tf.float32)
    
    # mul = multiply 是将input1和input2 做乘法运算，并输出为 output 
    ouput = tf.multiply(input1, input2)
    with tf.Session() as sess:
        print(sess.run(ouput, feed_dict={input1: [7.], input2: [2.]}))
    # [ 14.]`
### 激励函数--Activation Function
神经网络两三层--任意激励函数均可
卷积神经网络--推荐激励函数Relu
循环神经网络--添加激励函数tanh
