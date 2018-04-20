---
title: 用Python实现快速排序
date: 2018-04-02 09:49:34
tags: [数据结构,排序]
categories:
		- 数据结构
		- 排序
---
### 一、思想
		快速排序是对冒泡排序的一种改进，其基本思想是通过一趟排序将待排序记录分割成独立的两部分，一种一部分记录的关键字均比
	另一部分记录的关键字小，最后再分别对这两部分记录继续进行排序，以达到整个序列有序。
		一趟快读排序的具体做法是：附设两个指针low和high，初值分别为low和high，设枢轴记录关键字为pivotkey，则首先从high
	所指位置向前搜索找到第一个关键字小于pivotkey的记录和枢轴记录相互交换，然后从low所指位置起向后搜索，找到第一个关键字大
	于pivotkey的记录和枢轴记录相互交换，重复这两步直到low=high为止，过程如图。
![](http://p2lakvkq0.bkt.clouddn.com/quickSort.jpg)
### 二、用Python实现快速排序
	`
	#!/usr/bin/env python
	# coding:utf-8
	
	def quickSort(lists, left, right):
	    if left >= right:
	        return lists
	    key = lists[left]
	    low = left
	    high = right
	    print("111")
	    while left < right:
	        while (left < right and lists[right] >= key):
	            right -= 1
	        lists[left] = lists[right]
	        while (left < right and lists[left] <= key):
	            left += 1
	        lists[right] = lists[left]
	    lists[left] = key
	    print("key")
	    quickSort(lists, low, left - 1)
	    quickSort(lists, left + 1, high)
	    return lists
	
	
	lists = [59, 3, 6, 3, 46]
	print(quickSort(x, 0, 4))
`