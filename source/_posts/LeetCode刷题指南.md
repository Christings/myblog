---
title: LeetCode刷题指南
date: 2018-03-02 15:39:56
tags: [LeetCode]
categories:
		- 面试题
		- LeetCode
---
## 26. Remove Duplicates from Sorted Array（从已排序数组中移除重复元素）
原题地址：  
[https://leetcode.com/problems/remove-duplicates-from-sorted-array/description/](https://leetcode.com/problems/remove-duplicates-from-sorted-array/description/)  
题意：  

	Given a sorted array, remove the duplicates in-place such that each element appear only once and return the new length.

	Do not allocate extra space for another array, you must do this by modifying the input array in-place with O(1) extra memory.

Example:

	Given nums = [1,1,2],

	Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively.
	It doesn't matter what you leave beyond the new length.
翻译:

	给定一个已排序的数组，删除重复的元素，这样每个元素只出现一次，并且返回新的数组长度。

	不允许为另一个数组使用额外的空间，你必须就地以常量空间执行这个操作。
思路：

	采用两指针，一个指针i指向原数组需要判断的元素，一个指针j指向新数组新加入的元素。  
	由于是有序数组，因此只要判断原数组中的元素不同于新数组新加入的元素，就将原数组的元素和新数组后一元素交换位置即可，没有重复元素就是和自身交换位置。
Python3：

	`class Solution:
	    def removeDuplicates(self, nums):
	        """
	        :type nums: List[int]
	        :rtype: int
	        """
	        if len(nums)==0:
	            return 0
	        j=0
	        for i in range(1,len(nums)):
	            if nums[i]!=nums[j]:
	                nums[j+1]=nums[i]
	                j=j+1
	        return j+1`

## 39. Plus One（求加上一个数字后的结果）
原题地址：
[https://leetcode.com/problems/plus-one/description/](https://leetcode.com/problems/plus-one/description/)
题意：

	Given a non-negative integer represented as a non-empty array of digits, plus one to the integer.
	
	You may assume the integer do not contain any leading zero, except the number 0 itself.
	
	The digits are stored such that the most significant digit is at the head of the list.
翻译：

	给一个非负数来代表一个非空的数组，假定数组不包含任何前导0，除了0本身，输出该数字加1后
	得到的数组，数组的最高位存储在数组最前面。
	input：[9,9]
	output：[1,0,0]
思路：
	
	从数组最低位开始遍历，若该位加1不产生进位，将加1后的结果直接返回；
	若该位加1产生进位，则该位返回为0；
	注意像99+1=100的情况下，需要在数组最前面加1。
Python3:
	`class Solution:
	    def plusOne(self, digits):
	        """
	        :type digits: List[int]
	        :rtype: List[int]
	        """
	        length=len(digits)
	        i=length-1
	        while(i>=0):
	            if i==0:
	                if (digits[i]+1)>9:
	                    digits[i]=0
	                    result=[1]
	                    result.extend(digits) # 要用extend，而不能用append
	                    return result
	            if (digits[i]+1)<=9:
	                digits[i]+=1
	                return digits
	            else:
	                digits[i]=0
	                i-=1
	        return digits`

## 235. Maximum Subarray（求出最大的连续字串）
原题地址：  
[https://leetcode.com/problems/maximum-subarray/description/](https://leetcode.com/problems/maximum-subarray/description/)  
题意：

	Find the contiguous subarray within an array (containing at least one number) which has the largest sum.

	For example, given the array [-2,1,-3,4,-1,2,1,-5,4],
	the contiguous subarray [4,-1,2,1] has the largest sum = 6.
翻译：

	给你一串数，有正有负 要求出最大的连续子串。
思路：
	
	动态规划问题：
	遍历整个数组，在数组的每一个位置时，求出一个全局最大值L，代表以当前位置
	为结尾的最大字串，而G是当前位置的L和上一个位置L相比较后，取数值更大的。
	当遍历到i时，以i个为结尾的最大字串就是我们的L。
	数组[-2,1,-3,4,-1,2,1,-5,4]
	位置0：L=-2,G=-2
	位置1：L=1,G=1
	位置2：L=-2，G=1
	位置3：L=4，G=4
	位置4：L=3，G=4
	位置5：L=5，G=5
	位置6：L=6，G=6
	位置7：L=-1，G=6
	位置8：L=4，G=6
	最后，全局最大值G就是我们的结果。
Python3：
	`class Solution:
	    def maxSubArray(self, nums):
	        """
	        :type nums: List[int]
	        :rtype: int
	        """
	        l=g=-10000000000
			for n in nums:
				l=max(n,l+n)
				g=max(l,g)
			return g `
	



