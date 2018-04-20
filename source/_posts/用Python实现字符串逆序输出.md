---
title: 用Python实现字符串逆序输出
date: 2018-04-02 10:57:00
tags: Python
categories:
		- Python
---
### 方法一：利用字符串的切片来实现逆序

	def reverse1(str):
    	return str[::-1]

### 方法二：将字符串转换为列表后再使用reverse()
	def reverse2(str):
		str_list=list(str)
		str_list.reverse()  # 列表翻转
		return ''.join(str_list) # 将列表转换为字符串

	def reverse2(str):
		str_list=[]
		for each in str:
			str_list.append(each)
		str_list.reverse()
		return ''.join(str_list)
### 方法三：新建列表，从后往前添加元素
	def reverse3(str):
		str_list=[]
		for i in range(len(str)-1,-1,-1):
			str_list.append(str[i])
		return ''.join(str_list)	
### 方法四：递归
	def reverse4(str):
		if len(str)<=1:
			return str
		return str[-1]+reverse4(str[:-1])
### 方法五：借助collections模块extendleft()
	import collections

	def reverse5(str):
	    deque1 = collections.deque(str)
	    deque2=collections.deque()
	    for char in deque1:
	        deque2.extendleft(char)
	    return ''.join(deque2)
### 方法六：swap交换操作，以中间为基准，交换对称位置的字符
	def reverse5(str):
    str_list = list(str)
    if len(str_list) == 0 or len(str_list) == 1:
        return str
    i = 0
    length = len(str_list)
    while i < length / 2:
        str_list[i], str_list[length - i - 1] = str_list[length - i - 1], str_list[i]
        i += 1
    return ''.join(str_list)