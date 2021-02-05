---
title: LeetCode-2-栈-单调栈
date: 2020-11-13 15:41:07
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---

### 496. 下一个更大元素 I
    链接：https://leetcode-cn.com/problems/next-greater-element-i/

    给定两个 没有重复元素 的数组 nums1 和 nums2 ，其中nums1 是 nums2 的子集。找到 nums1 中每个元素在 nums2 中的下一个比其大的值。

    nums1 中数字 x 的下一个更大元素是指 x 在 nums2 中对应位置的右边的第一个比 x 大的元素。如果不存在，对应位置输出 -1 。

    示例 1:

    输入: nums1 = [4,1,2], nums2 = [1,3,4,2].
    输出: [-1,3,-1]
    解释:
        对于num1中的数字4，你无法在第二个数组中找到下一个更大的数字，因此输出 -1。
        对于num1中的数字1，第二个数组中数字1右边的下一个较大数字是 3。
        对于num1中的数字2，第二个数组中没有下一个更大的数字，因此输出 -1。
    示例 2:

    输入: nums1 = [2,4], nums2 = [1,2,3,4].
    输出: [3,-1]
    解释:
        对于 num1 中的数字 2 ，第二个数组中的下一个较大数字是 3 。
        对于 num1 中的数字 4 ，第二个数组中没有下一个更大的数字，因此输出 -1 。
     

    提示：

    nums1和nums2中所有元素是唯一的。
    nums1和nums2 的数组大小都不超过1000。

题解一|暴力：

时间复杂度：O(n^2)

从前向后遍历：
```
class Solution:
    def nextGreaterElement(self, nums1: List[int], nums2: List[int]) -> List[int]:
        m=len(nums1)
        n=len(nums2)
        res=[-1]*m
        for i in range(m):
            flag=0
            for j in range(n):
                if nums1[i]==nums2[j]:
                    flag=1
                if flag==1 and nums1[i]<nums2[j]:
                    res[i]=nums2[j]
                    break
        return res
```

从后向前遍历：
```
class Solution:
    def nextGreaterElement(self, nums1: List[int], nums2: List[int]) -> List[int]:
        m=len(nums1)
        n=len(nums2)
        res=[-1]*m
        for i in range(m):
            for j in range(n-1,-1,-1):
                if nums1[i]==nums2[j]:
                    break
                if nums1[i]<nums2[j]:
                    res[i]=nums2[j]
        return res
```

题解二|单调递减栈：

思路：

    遍历nums2，维护一个递减栈
    当得到一个更大的数的时候，将栈里小于它的数都放到哈希表当中

时间复杂度：O(M+N)
空间复杂度：O(M)
```
class Solution:
    def nextGreaterElement(self, nums1: List[int], nums2: List[int]) -> List[int]:
        m=len(nums1)
        n=len(nums2)
        hash={}
        stack=[]
        res=[]
        for i in range(n):
            while stack and stack[-1]<nums2[i]:
                hash[stack.pop()]=nums2[i] # hash中存储第一个比它大的数字
            stack.append(nums2[i])
        for i in nums1:
            res.append(hash.get(i,-1))
        return res 
```

### 503. 下一个更大元素 II
    链接：https://leetcode-cn.com/problems/next-greater-element-ii/

    给定一个循环数组（最后一个元素的下一个元素是数组的第一个元素），输出每个元素的下一个更大元素。数字 x 的下一个更大的元素是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出 -1。

    示例 1:

    输入: [1,2,1]
    输出: [2,-1,2]
    解释: 第一个 1 的下一个更大的数是 2；
    数字 2 找不到下一个更大的数； 
    第二个 1 的下一个最大的数需要循环搜索，结果也是 2。
    注意: 输入数组的长度不会超过 10000。

题解一|暴力：

```
class Solution:
    def nextGreaterElements(self, nums: List[int]) -> List[int]:
        new=nums+nums
        res=[-1]*len(new)
        for i in range(len(new)):
            tmp=new[i]
            if i>len(nums):
                break
            for j in range(i+1,len(new)):
                if new[i]<new[j]:
                    res[i]=new[j]
                    break
        return res[:len(nums)]
```

题解二|单调递减栈：

不同点：和上一题不相同的是，这个是循环数组，关于循环数组的一个技巧就是*2取余了

栈内存储的是什么，是下标还是值，都是可以的。

```
class Solution:
    def nextGreaterElements(self, nums: List[int]) -> List[int]:
        n=len(nums)
        stack=[]
        res=[-1]*n
        for i in range(2*n):
            while stack and nums[stack[-1]] < nums[i%n]:
                index=stack.pop()
                res[index%n]=nums[i%n]
            stack.append(i%n)
        return res
```
### 739. 每日温度
    链接：https://leetcode-cn.com/problems/daily-temperatures/

    请根据每日 气温 列表，重新生成一个列表。对应位置的输出为：要想观测到更高的气温，至少需要等待的天数。如果气温在这之后都不会升高，请在该位置用 0 来代替。

    例如，给定一个列表 temperatures = [73, 74, 75, 71, 69, 72, 76, 73]，你的输出应该是 [1, 1, 4, 2, 1, 1, 0, 0]。

    提示：气温 列表长度的范围是 [1, 30000]。每个气温的值的均为华氏度，都是在 [30, 100] 范围内的整数。

题解一|暴力|超时：

关键：找出右边第 1 个严格大于自己的元素的索引。

问题：就像选择排序一样，上一轮的操作并没有为下一轮的操作留下什么有用的信息。

时间复杂度：O(n^2)
空间复杂度：O(n)

```
class Solution:
    def dailyTemperatures(self, T: List[int]) -> List[int]:
        n=len(T)
        res=[0]*n
        for i in range(n-1):
            tmp=T[i]
            for j in range(i+1,n):
                if tmp<T[j]:
                    res[i]=j-i
                    break                
        return res    
```

题解二|单调递减栈：

单调栈介绍视频：https://leetcode-cn.com/problems/daily-temperatures/solution/leetcode-tu-jie-739mei-ri-wen-du-by-misterbooo/

时间复杂度：O(n)
空间复杂度：O(n)

```
class Solution:
    def dailyTemperatures(self, T: List[int]) -> List[int]:
        n=len(T)
        stack=[]
        res=[0]*n
        for i in range(n):
            while stack and T[stack[-1]]<T[i]:
                index=stack.pop()
                res[index]=i-index
            stack.append(i)
        return res    
```
