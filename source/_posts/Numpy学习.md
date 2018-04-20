---
title: Numpy学习
date: 2018-01-23 17:35:00
tags: numpy
categories: 
		- 数据分析
		- Numpy
---
> 本文首发于我的博客：[gongyanli.com](http://gongyanli.com/Numpy%E5%AD%A6%E4%B9%A0/)

> 参考视频：[莫烦的numpy视频/](https://morvanzhou.github.io/tutorials/data-manipulation/np-pd/)

前言：和pandas一样，numpy曾经也一起看得，还是忘记了。还是推荐那个视频，短小上手快。我写的东西有时候我会进行更新，所以有些写好的内容，后面也可能会加新内容，所以大家不要见怪啊。因为一般写东西numpy在前pandas在后，但是我好像是反着的，所以这个前言是我后来加的。因为强迫症的我，一篇文章有了前言，就觉得每篇都该有。
## 一、安装
	
	windows：pip install numpy(anaconda下)
	mac：pip install numpy
	linux:pip install numpy
## 二、导入
	
	import numpy as np
## 三、Numpy属性
	
	ndim:维度
	shape:行数和列数
	size：元素个数
## 四、Numpy--创建array
	
	array：创建数组
	dtype：指定数据类型
	zeros：创建数据全为0
	ones：创建数据全为1
	empty：创建数据接近0
	arrange：按指定范围创建数据
	linspace：创建线段

	a = np.array([2,23,4])  # list 1d
	print(a)
	# [2 23 4]

	a = np.array([2,23,4],dtype=np.int)  # np.int32/float/float32
	print(a.dtype)
	# int 64

	a = np.array([[2,23,4],[2,32,4]])  # 2d 矩阵 2行3列
	print(a)
	"""
	[[ 2 23  4]
	 [ 2 32  4]]
	"""

	a = np.zeros((3,4)) # 数据全为0，3行4列
	"""
	array([[ 0.,  0.,  0.,  0.],
	       [ 0.,  0.,  0.,  0.],
	       [ 0.,  0.,  0.,  0.]])
	"""
	

	a = np.ones((3,4),dtype = np.int)   # 数据为1，3行4列
	"""
	array([[1, 1, 1, 1],
	       [1, 1, 1, 1],
	       [1, 1, 1, 1]])
	"""
	
	a = np.empty((3,4)) # 数据为empty，3行4列
	"""
	array([[  0.00000000e+000,   4.94065646e-324,   9.88131292e-324,
	          1.48219694e-323],
	       [  1.97626258e-323,   2.47032823e-323,   2.96439388e-323,
	          3.45845952e-323],
	       [  3.95252517e-323,   4.44659081e-323,   4.94065646e-323,
	          5.43472210e-323]])
	"""

	a = np.arange(10,20,2) # 10-19 的数据，2步长
	"""
	array([10, 12, 14, 16, 18])
	"""

	a = np.arange(12).reshape((3,4))    # 3行4列，0到11
	"""
	array([[ 0,  1,  2,  3],
	       [ 4,  5,  6,  7],
	       [ 8,  9, 10, 11]])
	"""

	a = np.linspace(1,10,20)    # 开始端1，结束端10，且分割成20个数据，生成线段
	"""
	array([  1.        ,   1.47368421,   1.94736842,   2.42105263,
	         2.89473684,   3.36842105,   3.84210526,   4.31578947,
	         4.78947368,   5.26315789,   5.73684211,   6.21052632,
	         6.68421053,   7.15789474,   7.63157895,   8.10526316,
	         8.57894737,   9.05263158,   9.52631579,  10.        ])
	"""

	a = np.linspace(1,10,20).reshape((5,4)) # 更改shape
	"""
	array([[  1.        ,   1.47368421,   1.94736842,   2.42105263],
	       [  2.89473684,   3.36842105,   3.84210526,   4.31578947],
	       [  4.78947368,   5.26315789,   5.73684211,   6.21052632],
	       [  6.68421053,   7.15789474,   7.63157895,   8.10526316],
	       [  8.57894737,   9.05263158,   9.52631579,  10.        ]])
	"""

## 五、Numpy--基础运算

	a=np.array([10,20,30,40])   # array([10, 20, 30, 40])
	b=np.arange(4)              # array([0, 1, 2, 3])

	c=a-b  # array([10, 19, 28, 37])
	c=a+b   # array([10, 21, 32, 43])
	c=a*b   # array([  0,  20,  60, 120])
	c=b**2  # array([0, 1, 4, 9])  # 二次方
	c=10*np.sin(a)  
	# array([-5.44021111,  9.12945251, -9.88031624,  7.4511316 ])
	print(b<3)  
	# array([ True,  True,  True, False], dtype=bool)

	a=np.array([[1,1],[0,1]])
	b=np.arange(4).reshape((2,2))
	
	print(a)
	# array([[1, 1],
	#       [0, 1]])
	
	print(b)
	# array([[0, 1],
	#       [2, 3]])

	c_dot = np.dot(a,b)
	# array([[2, 4],
	#       [2, 3]])

	c_dot_2 = a.dot(b)
	# array([[2, 4],
	#       [2, 3]])

	a=np.random.random((2,4))
	print(a)
	# array([[ 0.94692159,  0.20821798,  0.35339414,  0.2805278 ],
	#       [ 0.04836775,  0.04023552,  0.44091941,  0.21665268]])

	np.sum(a)   # 4.4043622002745959
	np.min(a)   # 0.23651223533671784
	np.max(a)   # 0.90438450240606416

	print("sum =",np.sum(a,axis=1))
	# sum = [ 1.96877324  2.43558896]
	
	print("min =",np.min(a,axis=0))
	# min = [ 0.23651224  0.41900661  0.36603285  0.46456022]
	
	print("max =",np.max(a,axis=1))
	# max = [ 0.84869417  0.9043845 ]



	A = np.arange(2,14).reshape((3,4)) 

	# array([[ 2, 3, 4, 5]
	#        [ 6, 7, 8, 9]
	#        [10,11,12,13]])
	         
	print(np.argmin(A))    # 0
	print(np.argmax(A))    # 11
	print(np.mean(A))        # 7.5
	print(np.average(A))     # 7.5
	print(A.mean())          # 7.5
	print(A.median())       # 7.5
	print(np.cumsum(A)) 
	
	# [2 5 9 14 20 27 35 44 54 65 77 90]
	print(np.diff(A))    
	
	# [[1 1 1]
	#  [1 1 1]
	#  [1 1 1]]
	print(np.nonzero(A))    
	
	# (array([0,0,0,0,1,1,1,1,2,2,2,2]),array([0,1,2,3,0,1,2,3,0,1,2,3]))
	
	
	A = np.arange(14,2, -1).reshape((3,4)) 
	
	# array([[14, 13, 12, 11],
	#       [10,  9,  8,  7],
	#       [ 6,  5,  4,  3]])
	
	print(np.sort(A))    
	
	# array([[11,12,13,14]
	#        [ 7, 8, 9,10]
	#        [ 3, 4, 5, 6]])
	
	print(np.transpose(A))    
	print(A.T)
	
	# array([[14,10, 6]
	#        [13, 9, 5]
	#        [12, 8, 4]
	#        [11, 7, 3]])
	# array([[14,10, 6]
	#        [13, 9, 5]
	#        [12, 8, 4]
	#        [11, 7, 3]])
	
	print(A)
	# array([[14,13,12,11]
	#        [10, 9, 8, 7]
	#        [ 6, 5, 4, 3]])
	
	print(np.clip(A,5,9))    
	# array([[ 9, 9, 9, 9]
	#        [ 9, 9, 8, 7]
	#        [ 6, 5, 5, 5]])

## 六、Numpy--索引

	A = np.arange(3,15)

	# array([3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14])
	         
	print(A[3])    # 6

	A = np.arange(3,15).reshape((3,4))
	"""
	array([[ 3,  4,  5,  6]
	       [ 7,  8,  9, 10]
	       [11, 12, 13, 14]])
	"""
	         
	print(A[2])         
	# [11 12 13 14]

	print(A[1][1])      # 8
	print(A[1, 1])      # 8
	print(A[1, 1:3])    # [8 9]
	for row in A:
	    print(row)
	"""    
	[ 3,  4,  5, 6]
	[ 7,  8,  9, 10]
	[11, 12, 13, 14]
	"""
	for column in A.T:
	    print(column)
	"""  
	[ 3,  7,  11]
	[ 4,  8,  12]
	[ 5,  9,  13]
	[ 6, 10,  14]
	"""
	
	A = np.arange(3,15).reshape((3,4))
	         
	print(A.flatten())   
	# array([3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14])
	
	for item in A.flat:
	    print(item)
	    
	# 3
	# 4
	……
	# 14

## 七、Numpy--array合并
np.vstack()
	
	A = np.array([1,1,1])
	B = np.array([2,2,2])
	         
	print(np.vstack((A,B)))    # vertical stack
	"""
	[[1,1,1]
	 [2,2,2]]
	"""
	C = np.vstack((A,B))      
	print(A.shape,C.shape)

	# (3,) (2,3)

np.hstack()

	D = np.hstack((A,B))       # horizontal stack

	print(D)
	# [1,1,1,2,2,2]
	
	print(A.shape,D.shape)
	# (3,) (6,)
np.newaxis()
	print(A[np.newaxis,:])
	# [[1 1 1]]
	
	print(A[np.newaxis,:].shape)
	# (1,3)
	
	print(A[:,np.newaxis])
	"""
	[[1]
	[1]
	[1]]
	"""
	
	print(A[:,np.newaxis].shape)
	# (3,1)


	A = np.array([1,1,1])[:,np.newaxis]
	B = np.array([2,2,2])[:,np.newaxis]
	         
	C = np.vstack((A,B))   # vertical stack
	D = np.hstack((A,B))   # horizontal stack
	
	print(D)
	"""
	[[1 2]
	[1 2]
	[1 2]]
	"""
	
	print(A.shape,D.shape)
	# (3,1) (3,2)
np.concatenate()

	C = np.concatenate((A,B,B,A),axis=0)
	
	print(C)
	"""
	array([[1],
	       [1],
	       [1],
	       [2],
	       [2],
	       [2],
	       [2],
	       [2],
	       [2],
	       [1],
	       [1],
	       [1]])
	"""
	
	D = np.concatenate((A,B,B,A),axis=1)
	
	print(D)
	"""
	array([[1, 2, 2, 1],
	       [1, 2, 2, 1],
	       [1, 2, 2, 1]])
	"""
## 八、Numpy--array分割

	A = np.arange(12).reshape((3, 4))
	print(A)
	"""
	array([[ 0,  1,  2,  3],
	    [ 4,  5,  6,  7],
	    [ 8,  9, 10, 11]])
	"""

	print(np.split(A, 2, axis=1)) #从左往右分割，纵向分割
	"""
	[array([[0, 1],
	        [4, 5],
	        [8, 9]]), array([[ 2,  3],
	        [ 6,  7],
	        [10, 11]])]
	"""

	print(np.split(A, 3, axis=0)) #从上往下分割，横向分割
	
	# [array([[0, 1, 2, 3]]), array([[4, 5, 6, 7]]), array([[ 8,  9, 10, 11]])]

	print(np.array_split(A, 3, axis=1)) #不等量分割
	"""
	[array([[0, 1],
	        [4, 5],
	        [8, 9]]), array([[ 2],
	        [ 6],
	        [10]]), array([[ 3],
	        [ 7],
	        [11]])]
	"""

	print(np.vsplit(A, 3)) #等于 print(np.split(A, 3, axis=0))

	# [array([[0, 1, 2, 3]]), array([[4, 5, 6, 7]]), array([[ 8,  9, 10, 11]])]
	
	
	print(np.hsplit(A, 2)) #等于 print(np.split(A, 2, axis=1))
	"""
	[array([[0, 1],
	       [4, 5],
	       [8, 9]]), array([[ 2,  3],
	        [ 6,  7],
	        [10, 11]])]

八、Numpy--copy and deep copy

	a = np.arange(4)
	# array([0, 1, 2, 3])
	
	b = a
	c = a
	d = b
	# a、b、c、d中只要任何一个数组的元素发生变化，与之相等的元素会随之变化

	b=a.copy()
	# 当a发生变化时，b不会随之发生变化