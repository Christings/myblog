---
title: 用Python实现插入排序
date: 2018-04-10 17:37:26
tags: [数据结构,排序]
categories:
		- 数据结构
		- 排序
---
### 一、思想
		插入排序原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。其基本操作就是
	将一个数据插入到已经排好序的有序数据中，从而得到一个新的、个数加一的有序数据，算法适用于少量数据的排序，是稳定的排
	序方法，时间复杂度为O(n^2)。
### 二、步骤
	1. 从第一个元素开始，该元素可以认为已经被排序
	2. 取出下一个元素，在已经排序的元素序列中从后向前扫描
	3. 如果该元素（已排序）大于新元素，将该元素移到下一位置
	4. 重复步骤3，直到找到已排序的元素小于或者等于新元素的位置
	5. 将新元素插入到该位置后
	6. 重复步骤2~5
### 二、用Python实现插入排序
	`
	#!/usr/bin/env python
	# coding:utf-8
	
	def insertSort(lists):
	    for i in range(1, len(lists)):
	        key = lists[i]
	        j = i - 1
	        while j >= 0:
	            if lists[j] > key:
	                lists[j + 1] = lists[j]
	                lists[j] = key
	            j -= 1
	    return lists
	
	
	lists = [4, 6, 2, 4, 7, 3, 9, 2, 1]
	print(insertSort(lists))
`