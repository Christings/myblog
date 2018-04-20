---
title: 神经网络--Tensorflow
date: 2018-02-06 15:25:59
tags: TensorFlow
categories:
		- 深度学习
		- TensorFlow
---
参考：[莫烦TensorFlow](https://morvanzhou.github.io/tutorials/machine-learning/tensorflow/)

## 一、人工神经网络与生物神经网络
	
		900百亿神经细胞组成了我们的生物神经网络，而它是通过刺激来产生新的联结，让信号能够
	通过新的联结传递来形成反馈；
		人工神经网络一种典型模式就是通过训练数据来进行预测，而预测的结果不一定很好，所以就
	通过正向或是反向传播来更新神经元，从而再形成一个好的神经系统。其在本质上，是一个能
	让计算机处理和优化的数学模型。

		当今神经网络是是深度学习最流行的一种框架，它的基本原理就是梯度下降(Gradient Descent)
	机制，其中的梯度就是我们大学所学的求导求微分。一般来说, 神经网络是一连串神经层所组成的把输入进行加工再输出的系统。
		
## 二、神经网络--梯度下降(Gradient Descent)

	梯度下降中的梯度
