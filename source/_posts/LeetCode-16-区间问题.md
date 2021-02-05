---
title: LeetCode-16-区间问题
date: 2020-11-26 18:27:11
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
>https://labuladong.github.io/ebook/%E7%AE%97%E6%B3%95%E6%80%9D%E7%BB%B4%E7%B3%BB%E5%88%97/%E5%8C%BA%E9%97%B4%E9%97%AE%E9%A2%98%E5%90%88%E9%9B%86.html

### 56. 合并区间
    链接：https://leetcode-cn.com/problems/merge-intervals/

    给出一个区间的集合，请合并所有重叠的区间。

    示例 1:

    输入: intervals = [[1,3],[2,6],[8,10],[15,18]]
    输出: [[1,6],[8,10],[15,18]]
    解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
    示例 2:

    输入: intervals = [[1,4],[4,5]]
    输出: [[1,5]]
    解释: 区间 [1,4] 和 [4,5] 可被视为重叠区间。
    注意：输入类型已于2019年4月15日更改。 请重置默认代码定义以获取新方法签名。

    提示：

    intervals[i][0] <= intervals[i][1]

题解一：

时间复杂度：O(nlogn)，其中 n为区间的数量。除去排序的开销，我们只需要一次线性扫描，所以主要的时间开销是排序的 O(nlogn)。

空间复杂度：O(logn)，其中 n为区间的数量。这里计算的是存储答案之外，使用的额外空间。O(logn) 即为排序所需要的空间复杂度。

```
class Solution(object):
    def merge(self, intervals):
        """
        :type intervals: List[List[int]]
        :rtype: List[List[int]]
        """
        if not intervals:
            return
        intervals.sort(key=lambda x:(x[0],-x[1]))
        res=[]
        res.append(intervals[0])
        for i in range(1,len(intervals)):
            tmp=intervals[i]
            last=res[-1]
            if last[1] >= tmp[0]:
                last[1]=max(last[1],tmp[1])
            else:
                res.append(tmp)
        return res
```

### 57. 插入区间
    链接：https://leetcode-cn.com/problems/insert-interval/

    给出一个无重叠的 ，按照区间起始端点排序的区间列表。

    在列表中插入一个新的区间，你需要确保列表中的区间仍然有序且不重叠（如果有必要的话，可以合并区间）。

    示例 1：

    输入：intervals = [[1,3],[6,9]], newInterval = [2,5]
    输出：[[1,5],[6,9]]
    示例 2：

    输入：intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
    输出：[[1,2],[3,10],[12,16]]
    解释：这是因为新的区间 [4,8] 与 [3,5],[6,7],[8,10] 重叠。
     
    注意：输入类型已在 2019 年 4 月 15 日更改。请重置为默认代码定义以获取新的方法签名。

题解一|分段考察：

1、不重叠，位于newInterval的左侧
2、重叠
2、不重叠，位于newInterval的右侧

```
class Solution(object):
    def insert(self, intervals, newInterval):
        """
        :type intervals: List[List[int]]
        :type newInterval: List[int]
        :rtype: List[List[int]]
        """
        res=[]
        i,n=0,len(intervals)
        while i<n and intervals[i][1]<newInterval[0]:
            res.append(intervals[i])
            i+=1
        while i<n and intervals[i][0]<=newInterval[1]:
            newInterval[0]=min(newInterval[0],intervals[i][0])
            newInterval[1]=max(newInterval[1],intervals[i][1])
            i+=1
        res.append(newInterval)
        while i<n:
            res.append(intervals[i])
            i+=1
        return res
```
题解二|模拟：
```
class Solution(object):
    def insert(self, intervals, newInterval):
        """
        :type intervals: List[List[int]]
        :type newInterval: List[int]
        :rtype: List[List[int]]
        """
        left,right=newInterval
        flag=False
        ans=[]
        for low,high in intervals:
            if low>right: # 在插入区间右侧且无交集
                if not flag:
                    ans.append([left,right]) # 此处插入交集部分
                    flag=True
                ans.append([low,high])
            elif high<left: # 在插入区间左侧且无交集
                ans.append([low,high])
            else: # 与插入区间有交集，计算它们的并集
                left=min(left,low)
                right=max(right,high)
        if not flag: # 如果intervals为空时，此处插入newInterval
            ans.append([left,right])
        return ans
```

### 228. 汇总区间
    给定一个无重复元素的有序整数数组 nums 。

    返回 恰好覆盖数组中所有数字 的 最小有序 区间范围列表。也就是说，nums 的每个元素都恰好被某个区间范围所覆盖，并且不存在属于某个范围但不属于 nums 的数字 x 。

    列表中的每个区间范围 [a,b] 应该按如下格式输出：

    "a->b" ，如果 a != b
    "a" ，如果 a == b
     
    示例 1：

    输入：nums = [0,1,2,4,5,7]
    输出：["0->2","4->5","7"]
    解释：区间范围是：
    [0,2] --> "0->2"
    [4,5] --> "4->5"
    [7,7] --> "7"
    示例 2：

    输入：nums = [0,2,3,4,6,8,9]
    输出：["0","2->4","6","8->9"]
    解释：区间范围是：
    [0,0] --> "0"
    [2,4] --> "2->4"
    [6,6] --> "6"
    [8,9] --> "8->9"
    示例 3：

    输入：nums = []
    输出：[]
    示例 4：

    输入：nums = [-1]
    输出：["-1"]
    示例 5：

    输入：nums = [0]
    输出：["0"]
    
    提示：

    0 <= nums.length <= 20
    -231 <= nums[i] <= 231 - 1
    nums 中的所有值都 互不相同

题解一|双指针：

时间复杂度：O(N)
空间复杂度：O(1)

```
class Solution(object):
    def summaryRanges(self, nums):
        """
        :type nums: List[int]
        :rtype: List[str]
        """
        left,right=0,0
        nums.append(float('inf')) # 解决溢出问题
        res=[]
        for i in range(len(nums)-1):
            right=i
            if nums[i]+1 != nums[i+1]:
                if left==right:
                    res.append(str(nums[right])+'')
                else:
                    res.append(str(nums[left])+'->'+str(nums[right]))
                
                left=i+1
                right=i+1
        return res
```

```
class Solution(object):
    def summaryRanges(self, nums):
        """
        :type nums: List[int]
        :rtype: List[str]
        """
        if len(nums) == 0: 
            return []
        elif len(nums) == 1:
            return [str(nums[0])]
        result = []
        j = 0 
        for i in range(len(nums)):
            if i:
                if nums[i] - nums[i-1] != 1:
                    if j == i-1:
                        result.append(str(nums[j]))
                    else:
                        result.append(str(nums[j]) + '->' + str(nums[i-1]))
                    if i == len(nums)-1:
                        result.append(str(nums[i]))
                    j = i
            if i == len(nums)-1 and j < len(nums)-1:
                result.append(str(nums[j]) + '->' + str(nums[i]))
        return result
```

### 986. 区间列表的交集
    链接：https://leetcode-cn.com/problems/interval-list-intersections/

    给定两个由一些 闭区间 组成的列表，每个区间列表都是成对不相交的，并且已经排序。

    返回这两个区间列表的交集。

    （形式上，闭区间 [a, b]（其中 a <= b）表示实数 x 的集合，而 a <= x <= b。两个闭区间的交集是一组实数，要么为空集，要么为闭区间。例如，[1, 3] 和 [2, 4] 的交集为 [2, 3]。）

    示例：

    输入：A = [[0,2],[5,10],[13,23],[24,25]], B = [[1,5],[8,12],[15,24],[25,26]]
    输出：[[1,2],[5,5],[8,10],[15,23],[24,24],[25,25]]
     

    提示：

    0 <= A.length < 1000
    0 <= B.length < 1000
    0 <= A[i].start, A[i].end, B[i].start, B[i].end < 10^9

题解一|归并计算：

时间复杂度：O(M+N)，其中 M, N 分别是数组 A 和 B 的长度。

空间复杂度：O(M+N)，答案中区间数量的上限。

```
class Solution(object):
    def intervalIntersection(self, A, B):
        """
        :type A: List[List[int]]
        :type B: List[List[int]]
        :rtype: List[List[int]]
        """
        i,j=0,0
        res=[]
        while i<len(A) and j<len(B):
            a1,a2=A[i][0],A[i][1]
            b1,b2=B[j][0],B[j][1]

            if b2>=a1 and a2>=b1:
                res.append([max(a1,b1),min(a2,b2)]) # 交集计算
            if b2<a2:
                j+=1
            else:
                i+=1
        return res
```

### 1288. 删除被覆盖区间
    链接：https://leetcode-cn.com/problems/remove-covered-intervals/

    给你一个区间列表，请你删除列表中被其他区间所覆盖的区间。

    只有当 c <= a 且 b <= d 时，我们才认为区间 [a,b) 被区间 [c,d) 覆盖。

    在完成所有删除操作后，请你返回列表中剩余区间的数目。

    示例：

    输入：intervals = [[1,4],[3,6],[2,8]]
    输出：2
    解释：区间 [3,6] 被区间 [2,8] 覆盖，所以它被删除了。
     
    提示：​​​​​​

    1 <= intervals.length <= 1000
    0 <= intervals[i][0] < intervals[i][1] <= 10^5
    对于所有的 i != j：intervals[i] != intervals[j]

题解一:
    
    1、区间被完全覆盖
    2、两个区间相交，合并成一个大区间
    3、两个区间完全不相交

注意：对于这两个起点相同的区间，我们需要保证长的那个区间在上面（按照终点降序），这样才会被判定为覆盖，否则会被错误地判定为相交，少算一个覆盖区间。

```
class Solution(object):
    def removeCoveredIntervals(self, intervals):
        """
        :type intervals: List[List[int]]
        :rtype: int
        """
        intervals.sort(key=lambda x: (x[0],-x[1])) # 起点升序排序，起点相同时降序排序
        left,right=intervals[0][0],intervals[0][1] # 合并区间的起点和终点
        count=0
        for i in range(1,len(intervals)):
            tmp=intervals[i]
            if left<=tmp[0] and right>=tmp[1]: # 覆盖区间
                count+=1
            if right>=tmp[0] and right<=tmp[1]: # 相交区间，合并
                right=tmp[1]
            if right<tmp[0]: # 完全不相交区间，更新起点和终点
                left=tmp[0]
                right=tmp[1]
        return len(intervals)-count
```

题解二|贪心|扫描线：
```
class Solution(object):
    def removeCoveredIntervals(self, intervals):
        """
        :type intervals: List[List[int]]
        :rtype: int
        """
        intervals.sort(key=lambda x: (x[0],-x[1]))
        right=intervals[0][1]
        count=0
        for i in range(1,len(intervals)):
            if right>=intervals[i][1]:
                count+=1
            else:
                right=intervals[i][1]
        return len(intervals)-count
```