---
title: LeetCode-18-搜索和排序
date: 2020-10-29 21:34:51
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
### 33. 搜索旋转排序数组
    链接：https://leetcode-cn.com/problems/search-in-rotated-sorted-array/

    给你一个整数数组 nums ，和一个整数 target 。

    该整数数组原本是按升序排列，但输入时在预先未知的某个点上进行了旋转。（例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] ）。

    请你在数组中搜索 target ，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

     
    示例 1：

    输入：nums = [4,5,6,7,0,1,2], target = 0
    输出：4
    示例 2：

    输入：nums = [4,5,6,7,0,1,2], target = 3
    输出：-1
    示例 3：

    输入：nums = [1], target = 0
    输出：-1
     
    提示：

    1 <= nums.length <= 5000
    -10^4 <= nums[i] <= 10^4
    nums 中的每个值都 独一无二
    nums 肯定会在某个点上旋转
    -10^4 <= target <= 10^4

题解一|二分查找：

时间复杂度：O(logn)，其中 n 为 nums数组的大小。整个算法时间复杂度即为二分搜索的时间复杂度 O(logn)。

空间复杂度：O(1) 我们只需要常数级别的空间存放变量。

注意：if中很多等号的判断

```
class Solution(object):
    def search(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        if not nums:
            return -1
        left,right=0,len(nums)-1
        while left<=right:
            mid=left+(right-left)//2
            if nums[mid]==target:
                return mid
            elif nums[0]<=nums[mid] : # 此处加=号，解决case[3,1]和1
                if nums[0]<=target<nums[mid]: # 此处加=号，解决case[1,3,5]和1
                    right=mid-1
                else:
                    left=mid+1
            else:
                if nums[mid]<target<=nums[len(nums)-1]: # 此处加=号，解决case[1,3]和3
                    left=mid+1
                else:
                    right=mid-1
        return -1
```

### 34. 在排序数组中查找元素的第一个和最后一个位置
    链接：https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/

    给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。

    你的算法时间复杂度必须是 O(log n) 级别。

    如果数组中不存在目标值，返回 [-1, -1]。

    示例 1:

    输入: nums = [5,7,7,8,8,10], target = 8
    输出: [3,4]
    示例 2:

    输入: nums = [5,7,7,8,8,10], target = 6
    输出: [-1,-1]

题解一：

时间复杂度： O(n)
空间复杂度： O(1)
```
class Solution(object):
    def searchRange(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        res=[-1,-1]
        for i in range(len(nums)):
            if nums[i]==target:
                res[0]=i
                break
        if res[0]==-1:
            return res
        for i in range(len(nums)-1,-1,-1):
            if nums[i]==target:
                res[1]=i
                break
        return res
```

题解二|二分查找：

时间复杂度： O(logn)
空间复杂度： O(1)

```class Solution(object):
    def searchRange(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        if not nums:
            return [-1,-1]
        left,right=0,len(nums)-1
        while left<right:
            mid=left+(right-left)//2
            if nums[mid]>=target:
                right=mid
            else:
                left=mid+1
        if nums[left]!=target:
            return [-1,-1]
        start=left

        left,right=0,len(nums)-1
        while left<right:
            mid=(left+right+1)//2
            if nums[mid]<=target:
                left=mid
            else:
                right=mid-1
        if nums[left]!=target:
            return [-1,-1]
        end=right
        return [start,end]
```


### 75. 颜色分类
    链接：https://leetcode-cn.com/problems/sort-colors/

    给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

    此题中，我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

    注意:
    不能使用代码库中的排序函数来解决这道题。

    示例:

    输入: [2,0,2,1,1,0]
    输出: [0,0,1,1,2,2]
    进阶：

    一个直观的解决方案是使用计数排序的两趟扫描算法。
    首先，迭代计算出0、1 和 2 元素的个数，然后按照0、1、2的排序，重写当前数组。
    你能想出一个仅使用常数空间的一趟扫描算法吗？

题解一|重写数组：
```
class Solution:
    def sortColors(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        c0,c1,c2=0,0,0
        for i in nums:
            if i==0:
                c0+=1
            elif i==1:
                c1+=1
            elif i==2:
                c2+=1
        for i in range(len(nums)):
            if c0:
                nums[i]=0
                c0-=1
            elif c1:
                nums[i]=1
                c1-=1
            elif c2:
                nums[i]=2
                c2-=1
```

题解二|单指针：

时间复杂度：O(n)，其中 n 是数组 nums 的长度。
空间复杂度：O(1)。
```
class Solution:
    def sortColors(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        n=len(nums)
        ptr=0
        for i in range(n):
            if nums[i]==0:
                nums[i],nums[ptr]=nums[ptr],nums[i]
                ptr+=1
        for i in range(ptr,n):
            if nums[i]==1:
                nums[i],nums[ptr]=nums[ptr],nums[i]
                ptr+=1
```

题解三|双指针：

时间复杂度：O(n)，其中 n 是数组 nums 的长度。
空间复杂度：O(1)。

```
class Solution:
    def sortColors(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        n=len(nums)
        p0=p1=0
        for i in range(n):
            if nums[i]==1:
                nums[i],nums[p1]=nums[p1],nums[i]
                p1+=1
            elif nums[i]==0:
                nums[i],nums[p0]=nums[p0],nums[i]
                if p0 < p1:
                    nums[i],nums[p1]=nums[p1],nums[i]
                p0+=1
                p1+=1
```

```
class Solution:
    def sortColors(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        n=len(nums)
        p0,p2=0,n-1
        i=0
        while i<=p2:
            while i<=p2 and nums[i]==2:
                nums[i],nums[p2]=nums[p2],nums[i]
                p2-=1
            if nums[i]==0:
                nums[i],nums[p0]=nums[p0],nums[i]
                p0+=1
            i+=1
```

### 162. 寻找峰值
    峰值元素是指其值大于左右相邻值的元素。

    给定一个输入数组 nums，其中 nums[i] ≠ nums[i+1]，找到峰值元素并返回其索引。

    数组可能包含多个峰值，在这种情况下，返回任何一个峰值所在位置即可。

    你可以假设 nums[-1] = nums[n] = -∞。

    示例 1:

    输入: nums = [1,2,3,1]
    输出: 2
    解释: 3 是峰值元素，你的函数应该返回其索引 2。
    示例 2:

    输入: nums = [1,2,1,3,5,6,4]
    输出: 1 或 5 
    解释: 你的函数可以返回索引 1，其峰值元素为 2；
         或者返回索引 5， 其峰值元素为 6。
    说明:

    你的解法应该是 O(logN) 时间复杂度的。

题解一|线性扫描：

1、一直上坡
2、一直下坡
3、上坡和下坡

时间复杂度 : O(n)。 我们对长度为 n 的数组 nums 只进行一次遍历。
空间复杂度 : O(1)。 只使用了常数空间。
```
class Solution(object):
    def findPeakElement(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        for i in range(len(nums)-1):
            if nums[i]>nums[i+1]:
                return i
        return len(nums)-1
```

题解二|递归|二分查找：
时间复杂度 : O(logn)。每一步都将搜索空间减半。因此，总的搜索空间只需要 log(n) 步。其中 n 为 nums 数组的长度。
空间复杂度: O(logn)。每一步都将搜索空间减半。因此，总的搜索空间只需要 log(n) 步。于是，递归树的深度为 log(n)。

```
class Solution(object):
    def findPeakElement(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        return self.binarySearch(nums,0,len(nums)-1)
    def binarySearch(self,nums,left,right):
        if left==right:
            return left
        mid=left+(right-left)//2
        if nums[mid]>nums[mid+1]:
            return self.binarySearch(nums,left,mid)
        return self.binarySearch(nums,mid+1,right)
```

题解三|迭代|二分查找：
```
class Solution(object):
    def findPeakElement(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        left,right=0,len(nums)-1
        while left<right:
            mid=left+(right-left)//2
            if nums[mid]>nums[mid+1]:
                right=mid
            else:
                left=mid+1
        return left
   
```


### 179. 最大数
    链接：https://leetcode-cn.com/problems/largest-number/
    
    给定一组非负整数 nums，重新排列它们每个数字的顺序（每个数字不可拆分）使之组成一个最大的整数。

    注意：输出结果可能非常大，所以你需要返回一个字符串而不是整数。

    示例 1：

    输入：nums = [10,2]
    输出："210"
    示例 2：

    输入：nums = [3,30,34,5,9]
    输出："9534330"
    示例 3：

    输入：nums = [1]
    输出："1"
    示例 4：

    输入：nums = [10]
    输出："10"
    

    提示：

    1 <= nums.length <= 100
    0 <= nums[i] <= 109

题解一|重载运算符:
```
class StrLt(str):
    def __lt__(x,y):
        # 1、比较 ab 与 ba的大小，按降序排列；
        # 2、将数组转化为字符串。
        return x+y>y+x

class Solution(object):
    def largestNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: str
        """
        tostr=[str(i) for i in nums]
        tostr.sort(key=StrLt)
        return ''.join(tostr) if tostr[0] != '0' else '0'
```
### 215. 数组中的第K个最大元素
    在未排序的数组中找到第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

    示例 1:

    输入: [3,2,1,5,6,4] 和 k = 2
    输出: 5
    示例 2:

    输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
    输出: 4
    说明:

    你可以假设 k 总是有效的，且 1 ≤ k ≤ 数组的长度。

题解一|小顶堆：
```
class Solution(object):
    def __init__(self):
        self.heapList = [0]
        self.currentSize = 0

    def findKthLargest(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        self.buildHeap(nums[:k])
        # print(self.heapList)
        for i in range(k,len(nums)):
            if nums[i]>self.heapList[1]:
                self.heapList[1]=nums[i]
                self.percDown(1)
        return self.heapList[1]
    def percDown(self,i):
        while (i * 2) <= self.currentSize:
            mc = self.minChild(i)
            if self.heapList[i] > self.heapList[mc]:
                tmp = self.heapList[i]
                self.heapList[i] = self.heapList[mc]
                self.heapList[mc] = tmp
                # self.heapList[i],self.heapList[mc]=self.heapList[mc],self.heapList[i]
            i = mc

    def minChild(self,i):
        if i * 2 + 1 > self.currentSize:
            return i * 2
        else:
            if self.heapList[i*2] < self.heapList[i*2+1]:
                return i * 2
            else:
                return i * 2 + 1

    def delMin(self):
        retval = self.heapList[1]
        self.heapList[1] = self.heapList[self.currentSize]
        self.currentSize = self.currentSize - 1
        self.heapList.pop()
        self.percDown(1)
        return retval

    def buildHeap(self,alist):
        i = len(alist) // 2
        self.currentSize = len(alist)
        self.heapList = [0] + alist[:]
        while (i > 0):
            self.percDown(i)
            i = i - 1
```

### 240. 搜索二维矩阵 II
    链接：https://leetcode-cn.com/problems/search-a-2d-matrix-ii/

    编写一个高效的算法来搜索 m x n 矩阵 matrix 中的一个目标值 target 。该矩阵具有以下特性：

    每行的元素从左到右升序排列。
    每列的元素从上到下升序排列。
     
    示例 1：

    输入：matrix = [[1,4,7,11,15],[2,5,8,12,19],[3,6,9,16,22],[10,13,14,17,24],[18,21,23,26,30]], target = 5
    输出：true
    示例 2：

    输入：matrix = [[1,4,7,11,15],[2,5,8,12,19],[3,6,9,16,22],[10,13,14,17,24],[18,21,23,26,30]], target = 20
    输出：false
     
    提示：

    m == matrix.length
    n == matrix[i].length
    1 <= n, m <= 300
    -109 <= matix[i][j] <= 109
    每行的所有元素从左到右升序排列
    每列的所有元素从上到下升序排列
    -109 <= target <= 109

题解一|暴力：
```
class Solution:
    def searchMatrix(self, matrix, target):
        """
        :type matrix: List[List[int]]
        :type target: int
        :rtype: bool
        """
        for row in matrix:
            if target in row:
                return True
        return False
```

题解二|二分查找：

时间复杂度：O(mlogn)
空间复杂度：O(1)
```
class Solution(object):
    def searchMatrix(self, matrix, target):
        """
        :type matrix: List[List[int]]
        :type target: int
        :rtype: bool
        """
        for i in range(len(matrix)):
            if self.binarySearch(matrix,i,target):
                return True
        return False

    def binarySearch(self,matrix,i,target):
        left,right=0,len(matrix[0])-1
        while left<=right:
            mid=left+(right-left)//2
            if target > matrix[i][mid]:
                left=mid+1
            elif target < matrix[i][mid]:
                right=mid-1
            elif target == matrix[i][mid]:
                return True
        return False
```

题解三|规律:

时间复杂度：O(m+n)
空间复杂度：O(1)

```
class Solution(object):
    def searchMatrix(self, matrix, target):
        """
        :type matrix: List[List[int]]
        :type target: int
        :rtype: bool
        """
        left,right=0,len(matrix[0])-1
        while left<len(matrix) and right>=0:
            if matrix[left][right]>target:
                right-=1
            elif matrix[left][right]<target:
                left+=1
            elif matrix[left][right]==target:
                return True
        return False
```


### 278. 第一个错误的版本
    链接：https://leetcode-cn.com/problems/first-bad-version/

    你是产品经理，目前正在带领一个团队开发新的产品。不幸的是，你的产品的最新版本没有通过质量检测。由于每个版本都是基于之前的版本开发的，所以错误的版本之后的所有版本都是错的。

    假设你有 n 个版本 [1, 2, ..., n]，你想找出导致之后所有版本出错的第一个错误的版本。

    你可以通过调用 bool isBadVersion(version) 接口来判断版本号 version 是否在单元测试中出错。实现一个函数来查找第一个错误的版本。你应该尽量减少对调用 API 的次数。

    示例:

    给定 n = 5，并且 version = 4 是第一个错误的版本。

    调用 isBadVersion(3) -> false
    调用 isBadVersion(5) -> true
    调用 isBadVersion(4) -> true

    所以，4 是第一个错误的版本。 

题解一|二分查找：
```
# The isBadVersion API is already defined for you.
# @param version, an integer
# @return an integer
# def isBadVersion(version):

class Solution:
    def firstBadVersion(self, n):
        """
        :type n: int
        :rtype: int
        """
        left,right=1,n
        while left<=right: # 注意是小于等于
            mid=left+(right-left)//2
            if isBadVersion(mid):
                right=mid-1
            else:
                left=mid+1
        return left
```

### 347. 前 K 个高频元素
    链接：https://leetcode-cn.com/problems/top-k-frequent-elements/

    给定一个非空的整数数组，返回其中出现频率前 k 高的元素。

    示例 1:

    输入: nums = [1,1,1,2,2,3], k = 2
    输出: [1,2]
    示例 2:

    输入: nums = [1], k = 1
    输出: [1]
    

    提示：

    你可以假设给定的 k 总是合理的，且 1 ≤ k ≤ 数组中不相同的元素的个数。
    你的算法的时间复杂度必须优于 O(n log n) , n 是数组的大小。
    题目数据保证答案唯一，换句话说，数组中前 k 个高频元素的集合是唯一的。
    你可以按任意顺序返回答案。

题解一|小根堆：
```
import collections 
class Solution(object):
    def __init__(self):
        self.size=0
        self.heap=[(0,0)]

    def topKFrequent(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: List[int]
        """
        hash={}
        for i in nums:
            hash[i]=hash.get(i,0)+1
        hashlist=[[key,value] for key,value in hash.items()]
        self.buildHeap(hashlist[:k]) # 建立基于次数的小根堆
        for i in range(k,len(hashlist)):
            if self.heap[1][1]<=hashlist[i][1]:
                self.heap[1]=hashlist[i]
                self.percDown(1)
        return [self.heap[i][0] for i in range(1,k+1)]

    def percDown(self,i):
        while (i * 2) <= self.size:
            mc = self.minChild(i)
            if self.heap[i][1] > self.heap[mc][1]: # 注意，基于次数
                self.heap[i],self.heap[mc]=self.heap[mc],self.heap[i]
            i = mc

    def minChild(self,i):
        if i * 2 + 1 > self.size:
            return i * 2
        else:
            if self.heap[i*2][1] < self.heap[i*2+1][1]: # 注意此处比较的是次数，而不是self.heap[i*2] < self.heap[i*2+1]
                return i * 2
            else:
                return i * 2 + 1
    def buildHeap(self,arr):
        i=len(arr)//2
        self.size=len(arr)
        self.heap=[(0,0)]+arr[:]
        while i>0:
            self.percDown(i)
            i-=1
```

### 451. 根据字符出现频率排序
    链接：https://leetcode-cn.com/problems/sort-characters-by-frequency/

    给定一个字符串，请将字符串里的字符按照出现的频率降序排列。

    示例 1:

    输入:
    "tree"

    输出:
    "eert"

    解释:
    'e'出现两次，'r'和't'都只出现一次。
    因此'e'必须出现在'r'和't'之前。此外，"eetr"也是一个有效的答案。
    示例 2:

    输入:
    "cccaaa"

    输出:
    "cccaaa"

    解释:
    'c'和'a'都出现三次。此外，"aaaccc"也是有效的答案。
    注意"cacaca"是不正确的，因为相同的字母必须放在一起。
    示例 3:

    输入:
    "Aabb"

    输出:
    "bbAa"

    解释:
    此外，"bbaA"也是一个有效的答案，但"Aabb"是不正确的。
    注意'A'和'a'被认为是两种不同的字符。

题解一|小根堆：
```
class Solution(object):
    def __init__(self):
        self.size=0
        self.heap=[['0',0]]
    def frequencySort(self, s):
        """
        :type s: str
        :rtype: str
        """
        hash={}
        for i in s:
            if i not in hash:
                hash[i]=1
            else:
                hash[i]+=1
        hashlist=[[key,value] for key,value in hash.items()]
        self.buildHeap(hashlist)

        for i in range(len(hashlist),0,-1): 不要写成self.size,和下面self.size-=1不好区分。
        # for i in range(self.size,0,-1):
            self.heap[1],self.heap[i]=self.heap[i],self.heap[1]
            self.size-=1 # 关键点,减1后避免和最后一个元素再交换回去。
            self.percDown(1)
        result=''
        for i in range(1,len(self.heap)):
            result+=str(self.heap[i][0]*self.heap[i][1])
        return result
    def percDown(self,i):
        while (i*2)<=self.size:
            mc=self.minChild(i)
            if self.heap[i][1]>self.heap[mc][1]:
                self.heap[i],self.heap[mc]=self.heap[mc],self.heap[i]
            i=mc
    def minChild(self,i):
        if i*2+1>self.size:
            return i*2
        else:
            if self.heap[i*2][1]<self.heap[i*2+1][1]:
                return i*2
            else:
                return i*2+1
    def buildHeap(self,arr):
        i=len(arr)//2
        self.size=len(arr)
        self.heap=[['0',0]]+arr
        while i>0:
            self.percDown(i)
            i-=1
```

### 973. 最接近原点的 K 个点
    链接：https://leetcode-cn.com/problems/k-closest-points-to-origin/

    我们有一个由平面上的点组成的列表 points。需要从中找出 K 个距离原点 (0, 0) 最近的点。

    （这里，平面上两点之间的距离是欧几里德距离。）

    你可以按任何顺序返回答案。除了点坐标的顺序之外，答案确保是唯一的。

    示例 1：

    输入：points = [[1,3],[-2,2]], K = 1
    输出：[[-2,2]]
    解释： 
    (1, 3) 和原点之间的距离为 sqrt(10)，
    (-2, 2) 和原点之间的距离为 sqrt(8)，
    由于 sqrt(8) < sqrt(10)，(-2, 2) 离原点更近。
    我们只需要距离原点最近的 K = 1 个点，所以答案就是 [[-2,2]]。
    示例 2：

    输入：points = [[3,3],[5,-1],[-2,4]], K = 2
    输出：[[3,3],[-2,4]]
    （答案 [[-2,4],[3,3]] 也会被接受。）
    

    提示：

    1 <= K <= points.length <= 10000
    -10000 < points[i][0] < 10000
    -10000 < points[i][1] < 10000

题解一|大根堆：
```
import math
class Solution(object):
    def __init__(self):
        self.size=0
        self.heap=[[0,0]]
    def kClosest(self, points, K):
        """
        :type points: List[List[int]]
        :type K: int
        :rtype: List[List[int]]
        """
        self.buildHeap(points[:K])
        for i in range(K,len(points)):
            if self.cal(self.heap[1]) > self.cal(points[i]):
                self.heap[1]=points[i]
                self.percDown(1)
        return [self.heap[i] for i in range(1,K+1)]

    def percDown(self,i):
        while i*2<=self.size:
            mc=self.maxChild(i)
            if self.cal(self.heap[i])<self.cal(self.heap[mc]):
                self.heap[i],self.heap[mc]=self.heap[mc],self.heap[i]
            i=mc
    def maxChild(self,i):
        if (i*2+1)>self.size:
            return i*2
        else:
            if self.cal(self.heap[i*2])>self.cal(self.heap[i*2+1]):
                return i*2
            else:
                return i*2+1
    def buildHeap(self,arr):
        i=len(arr)//2
        self.heap=[[0,0]]+arr
        self.size=len(arr)
        while i>0:
            self.percDown(i)
            i-=1
    
    def cal(self,point):
        return math.sqrt(point[0]**2+point[1]**2)
```