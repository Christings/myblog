---
title: TensorFlow学习
date: 2018-03-05 10:07:15
tags:
---
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