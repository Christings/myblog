---
title: 用Python实现选择排序
date: 2018-04-12 17:11:25
tags: [数据结构]
categories:
		- 数据结构
		- 排序
---
### 一、思想
	第1趟，在待排序记录r1 ~ r[n]中选出最小的记录，将它与r1交换；
	第2趟，在待排序记录r2 ~ r[n]中选出最小的记录，将它与r2交换；
	以此类推，第i趟在待排序记录r[i] ~ r[n]中选出最小的记录，将它与r[i]交换，使有序序列不断增长直到全部排序完毕。
	原始数据：[3,1,0,8,4,2]
	第一趟：[0,1,3,8,4,2]
	第二趟：[0,1,3,8,4,2]
	第三趟：[0,1,2,8,4,3]
	第四趟：[0,1,2,3,4,8]
	第五趟：[0,1,2,3,4,8]
### 二、用Python实现选择排序
	`
	def selectSort(nums):
	    for i in range(len(nums)-1):
	        print(i)
	        min = i
	        for j in range(i + 1, len(nums)):
	            if nums[min] > nums[j]:
	                min = j
	        nums[i], nums[min] = nums[min], nums[i]
	    return nums`