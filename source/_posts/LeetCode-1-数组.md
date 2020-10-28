---
title: LeetCode-1-数组
date: 2019-09-26 12:13:05
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
## 一、数组
### 1.两数之和
    链接：https://leetcode-cn.com/problems/two-sum/

    给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

    你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

    示例:

    给定 nums = [2, 7, 11, 15], target = 9

    因为 nums[0] + nums[1] = 2 + 7 = 9
    所以返回 [0, 1]

题解一|暴力：

最简单的思路就是通过两遍循环直接进行暴力破解，时间复杂度O(n^2)，空间复杂度O(1)。
```
 class Solution:
    def twoSum(self,nums,target):
        for i in range(0,len(nums)):
            for j in range(i+1,len(nums)):
                if nums[j]==target-nums[i]:
                    return i,j
```

题解二|hash：

接着思考如何进行优化，采用hash记录需要的key，遍历的时候找到需要的值。时间复杂度O(n)，空间复杂度O(n)。
```
 class Solution:
    def twoSum(self,nums,target):
        dicts={}
        for i in range(len(nums)):
            tmp=nums[i]
            if target-tmp in dicts:
                return (dicts[target-tmp],i)
            dicts[tmp]=i
```

```
 class Solution:
    def twoSum(self,nums,target):
        dicts={}
        for i,num in enumerate(nums):
            if num in dicts:
                return [dicts[num],i]
            else:
                dicts[target-num]=i
```

题解三|切片：

判断target-nums[i]是否在nums[i+1:]中，时间复杂度O(n)，空间复杂度O(1)。
```
 class Solution:
    def twoSum(self,nums,target):
        for i in range(len(nums)):
            if target-nums[i] in nums[i+1:]:
                return [i,nums.index(target-nums[i],i+1)]
```

index()方法语法：list.index(x[, start[, end]])
参数:
    x-- 查找的对象。
    start-- 可选，查找的起始位置。
    end-- 可选，查找的结束位置。
返回值:该方法返回查找对象的索引位置，如果没有找到对象则抛出异常。

### 2.两数相加
    链接：
    https://leetcode-cn.com/problems/add-two-numbers/

    给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

    如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

    您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

    示例：

    输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
    输出：7 -> 0 -> 8
    原因：342 + 465 = 807

题解一：
```
class ListNode:
    def __init__(self,x):
        self.val=x
        self.next=None
class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        if l1 is None:
            return l2
        if l2 is None:
            return l1

        tmp=ListNode(0)
        res=tmp
        flag=0

        while l1 or l2:
            tmpSum=0
            if l1:
                tmpSum=l1.val
                l1=l1.next
            if l2:
                tmpSum+=l2.val
                l2=l2.next
            tmpRes=(tmpSum+falg)%10
            flag=(tmpSum+flag)//10

            res.next=ListNode(tmpRes)
            res=res.next # res后移

            # 这个判断是拿来解决此种情况的：
            # 输入：【5】和【5】
            # 输出：【0,1】
            if flag:
                res.next=ListNode(1) # 这里创建了节点，但是没有后移

        res=tmp.next # 把链表初始化的0后移
        return res
```

```
class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        res=dummy=ListNode(-1)
        flag=0
        while l1 and l2:
            res.next=ListNode(l1.val+l2.val+flag)
            flag=res.next.val // 10
            res.next.val %= 10
            res=res.next
            l1=l1.next
            l2=l2.next
        
        p= l1 or l2
        while p:
            res.next=ListNode(p.val+flag)
            flag=res.next.val // 10
            res.next.val %= 10
            p=p.next
            res=res.next
        if flag:
            res.next=ListNode(1)
        return dummy.next
```

### ？？4.寻找两个有序数组的中位数
    链接：https://leetcode-cn.com/problems/median-of-two-sorted-arrays/

    给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。

    请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

    你可以假设 nums1 和 nums2 不会同时为空。

    示例 1:

    nums1 = [1, 3]
    nums2 = [2]

    则中位数是 2.0
    示例 2:

    nums1 = [1, 2]
    nums2 = [3, 4]

    则中位数是 (2 + 3)/2 = 2.5

题解一（暴力）：


### 6.Z字形变换
    链接：https://leetcode-cn.com/problems/zigzag-conversion/

    将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

    比如输入字符串为 "LEETCODEISHIRING" 行数为 3 时，排列如下：

    L   C   I   R
    E T O E S I I G
    E   D   H   N
    之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如："LCIRETOESIIGEDHN"。

    请你实现这个将字符串进行指定行数变换的函数：

    string convert(string s, int numRows);
    示例 1:

    输入: s = "LEETCODEISHIRING", numRows = 3
    输出: "LCIRETOESIIGEDHN"
    示例 2:

    输入: s = "LEETCODEISHIRING", numRows = 4
    输出: "LDREOEIIECIHNTSG"
    解释:

    L     D     R
    E   O E   I I
    E C   I H   N
    T     S     G

题解一：
![](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/z-word.png)
![](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/z-word1.png)
```
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows<2:
            return s
        ans=['' for _ in range(numRows)]
        i,flag=0,-1
        for c in s:
            ans[i] += c
            if i==0 or i==numRows-1:
                flag=-flag
            i+=flag
        return ''.join(ans)

```

### 7.整数反转
    链接：https://leetcode-cn.com/problems/reverse-integer/

    给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

    示例 1:

    输入: 123
    输出: 321
    示例 2:

    输入: -123
    输出: -321
    示例 3:

    输入: 120
    输出: 21
    注意:

    假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−2^31,  2^31 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。
题解一：
int型的数值范围是 -2147483648～2147483647， 那么如果我们要翻转 1000000009 这个在范围内的数得到 9000000001，而翻转后的数就超过了范围。
```
class Solution:
    def reverse(self, x: int) -> int:
        x=int(str(x)[::-1]) if x>=0 else -int(str((-x)[::-1])) # python3没有数字范围，如果其他语言之间int会造成精度缺失。
        # return x if x<2**31-1 and x>-2**31 else 0
        return x if x<pow(2,31)-1 and x>-pow(2,31) else 0
```
题解二：
    # /:除法
    # //:整除
    # %：取模
```
class Solution:
    def reverse(self, x):
        flag = 1 if x >= 0 else -1
        new_x, x = 0, abs(x)
        while x:
            new_x = 10 * new_x + x % 10
            x //= 10
        new_x = flag * new_x
        return new_x if new_x < 2147483648 and new_x >= -2147483648 else 0
```
### 8.字符串转换整数(atoi)
    链接：https://leetcode-cn.com/problems/string-to-integer-atoi/

    请你来实现一个 atoi 函数，使其能将字符串转换成整数。

    首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。

    当我们寻找到的第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字组合起来，作为该整数的正负号；假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成整数。

    该字符串除了有效的整数部分之后也可能会存在多余的字符，这些字符可以被忽略，它们对于函数不应该造成影响。

    注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换。

    在任何情况下，若函数不能进行有效的转换时，请返回 0。

    说明：

    假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 [−231,  231 − 1]。如果数值超过这个范围，请返回  INT_MAX (231 − 1) 或 INT_MIN (−231) 。

    示例 1:

    输入: "42"
    输出: 42
    示例 2:

    输入: "   -42"
    输出: -42
    解释: 第一个非空白字符为 '-', 它是一个负号。
         我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。
    示例 3:

    输入: "4193 with words"
    输出: 4193
    解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。
    示例 4:

    输入: "words and 987"
    输出: 0
    解释: 第一个非空字符是 'w', 但它不是数字或正、负号。
         因此无法执行有效的转换。
    示例 5:

    输入: "-91283472332"
    输出: -2147483648
    解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 
          因此返回 INT_MIN (−231) 。
题解一|正则表达式：

    ^：匹配字符串开头
    [\+\-]：代表一个+字符或-字符
    ?：前面一个字符可有可无
    \d：一个数字
    +：前面一个字符的一个或多个
    \D：一个非数字字符
    *：前面一个字符的0个或多个

```
class Solution:
    def myAtoi(self, str: str) -> int:
        return max(min(int(*re.findall('^[\+\-]?\d+',str.lstrip())),2**31-1),-2**31)
        #由于返回的是个列表，解包并且转换成整数
```

```
class Solution:
    def myAtoi(self, str: str) -> int:
        # return max(min(int(*re.findall('^[\+\-]?\d+',str.lstrip())),2**31-1),-2**31)
        # print(re.findall('\d',str.lstrip())) # ['4', '2', '4', '4']
        # print(re.findall('\d*',str.lstrip())) # ['4244', '']
        # print(re.findall('\d+',str.lstrip())) # ['4244']
        # print(re.findall('^[+-]\d+',str.lstrip())) # ['-4244']
        # print(re.findall('^[+-]?\d+',str.lstrip())) # ?代表前面的+-可有可无。
        # #  re.findall返回的是一个字符串列表，int()数据类型转换不支持列表，用*对列表解包得到字符串。比如[7,8,8]→7,8,8
        # print(int(*re.findall('^[+-]\d+',str.lstrip()))) # -4244

        x=int(*re.findall('^[-+]?\d+',str.lstrip()))
        if x > pow(2,31)-1:
            return pow(2,31)-1
        elif x < pow(-2,31):
            return pow(-2,31)
        else:
            return x
```

题解二|逐个强行转int：
```
class Solution:
    def myAtoi(self, str: str) -> int:
        str=str.lstrip()

        if len(str)==0:
            return 0
        last=0
        i=1 if str[0]=='+' or str[0]=='-' else 0
        #循环，直到无法强转成int，跳出循环
        while i<=len(str):
            try:
                last=int(str[:i+1]) # int['aa']会报错，所以走break
                i+=1
            except:
                break
        # 判断数字是否超出范围
        if last<-2**31:
            return -2**31
        if last>2**31-1:
            return 2**31-1
        return last
    
```
### 9.回文数
    链接：https://leetcode-cn.com/problems/palindrome-number/

    判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

    示例 1:

    输入: 121
    输出: true
    示例 2:

    输入: -121
    输出: false
    解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
    示例 3:

    输入: 10
    输出: false
    解释: 从右向左读, 为 01 。因此它不是一个回文数。

题解一|整数转换为字符串:
```
class Solution:
    def isPalindrome(self, x: int) -> bool:
        x=str(x)
        if x == x[::-1]:
            return True
        else:
            return False

class Solution:
    def isPalindrome(self, x) :
        x=str(x)
        return x==x[::-1]

# 整数转字符串，通过下标对比确定该整数是否为回文数
class Solution:
    def isPalindrome(self, x) :
        x=str(x)
        for i in range(0,int(len(x)/2)):
            if x[i] != x[-i-1]:
                return False
        return True
```
题解二（数字反转）:
```
class Solution:
    def isPalindrome(self, x) :
        if x<0:
            return False
        m,n=x,0

        while m:
            n=n*10+m%10
            m=m//10
        if x==n:
            return True
        else:
            return False
```
### 11.盛最多水的容器
    链接：https://leetcode-cn.com/problems/container-with-most-water/

    给定 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

    说明：你不能倾斜容器，且 n 的值至少为 2。
![](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/youdao/water3.jpg)

    图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。

    示例:

    输入: [1,8,6,2,5,4,8,3,7]
    输出: 49
思路：
    矩阵的面积=矩阵长度 * 矩阵宽度
    矩阵长度：两条垂直线的距离
    矩阵宽度：两条垂直线中较短一条的长度
    面积最大化：两条垂直线的距离越远越好，两条垂直线的最短长度也要越长越好。

解法一|暴力|超时：
    在这种情况下，我们将简单地考虑每对可能出现的线段组合并找出这些情况之下的最大面积。
    时间复杂度O(n^2)，计算n(n-1)/2种高度组合的面积，空间复杂度O(1)。
```
不能通过case:[1,1],结果应该为1.
class Solution:
    def maxArea(self, height: List[int]) -> int:
        area=0
        for i in range(len(height)):
            for j in range(i+1,len(height)):
                # print('i',i,height[i])
                # print('j',j,height[j])
                area=max(area,min(height[i],height[j])*(j-1)) # j-1为矩阵的长度
```

```
class Solution:
    def maxArea(self, height: List[int]) -> int:
        area=0
        for i in range(len(height)-1):
            for j in range(i+1,len(height)):
                # print('i',i,height[i])
                # print('j',j,height[j])
                area=max(area,min(height[i],height[j])*(j-i)) # j-i为矩阵的长度
        return area
```

解法二|双指针：

    设置两个指针 left 和 right，分别指向数组的最左端和最右端。此时，两条垂直线的距离是最远的，若要下一个矩阵面积比当前面积来得大，必须要把 height[left] 和 height[right] 中较短的垂直线往中间移动，看看是否可以找到更长的垂直线。
    时间复杂度O(n)，空间复杂度O(1)。
```
class Solution:
    def maxArea(self, height: List[int]) -> int:
        left=0
        right=len(height)-1
        area=0

        while left < right:
            cur=min(height[left],height[right]) * (right-left)
            area=max(area,cur)

            if height[left]<height[right]:
                left+=1
            else:
                right-=1
        return area
```

### 15.三数之和
    链接：https://leetcode-cn.com/problems/3sum/

    给定一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？找出所有满足条件且不重复的三元组。

    注意：答案中不可以包含重复的三元组。

    例如, 给定数组 nums = [-1, 0, 1, 2, -1, -4]，

    满足要求的三元组集合为：
    [
      [-1, 0, 1],
      [-1, -1, 2]
    ]

题解一|暴力：
    使用三重循环进行暴力破解，时间复杂度为O(n^3)，空间复杂度O(n)

题解二|排序+双指针：

    继续思考如何优化，可以通过双指针动态消去无效解来优化效率。
    铺垫：先将给定 nums 排序，复杂度为 O(nlogn)
    思路：固定 3 个指针中最左（最小）数字的指针 k，双指针 i，j 分设在数组索引 (k, len(nums))两端，通过双指针交替向中间移动，记录对于每个固定指针 k 的所有满足 nums[k] + nums[i] + nums[j] == 0 的 i,j 组合。
    时间复杂度O(n^2)，空间复杂度O(n)。

```
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        lens=len(nums)
        res=[]

        for k in range(lens):
            if k>0 and nums[k]==nums[k-1]:
                continue

            i,j=k+1,lens-1
            while i<j:
                sum=nums[k]+nums[i]+nums[j]
                if sum==0:
                    tmp=[nums[k],nums[i],nums[j]]
                    res.append(tmp)

                    while i<j and nums[i]==nums[i+1]:
                        i+=1
                    while i<j and nums[j]==nums[j-1]:
                        j-=1

                    i+=1
                    j-=1

                elif sum<0:
                    i+=1
                else:
                    j-=1

        return res
```
### 16.最接近的三数之和
    链接：https://leetcode-cn.com/problems/3sum-closest/

    给定一个包括 n 个整数的数组 nums 和 一个目标值 target。找出 nums 中的三个整数，使得它们的和与 target 最接近。返回这三个数的和。假定每组输入只存在唯一答案。

    例如，给定数组 nums = [-1，2，1，-4], 和 target = 1.

    与 target 最接近的三个数的和为 2. (-1 + 2 + 1 = 2).

题解一|排序+双指针：

思路类似于三数之和，首先将数组进行排序，同时在左右指针运动过程中，记录与 target 绝对值差值最小的三数之和。

时间复杂度：O(nlogn) + O(n^2) = O(n^2)，空间复杂度O(1)
```
class Solution:
    def threeSumClosest(self, nums: List[int], target: int) -> int:
        nums.sort()
        length=len(nums)
        res=float('inf')
        
        for k in range(length):
            # if k>0 and nums[k]==nums[k-1]:
            #    continue
            
            i,j=k+1,length-1
            while i<j:
                sum=nums[k]+nums[i]+nums[j]
                if sum==target:
                    return target
                
                if abs(res-target)>abs(sum-target):
                    res=sum
                    
                if sum<target:
                    i+=1
                else:
                    j-=1
        return res
```
### 18.四数之和
    链接：https://leetcode-cn.com/problems/4sum/

    给定一个包含 n 个整数的数组 nums 和一个目标值 target，判断 nums 中是否存在四个元素 a，b，c 和 d ，使得 a + b + c + d 的值与 target 相等？找出所有满足条件且不重复的四元组。

    注意：

    答案中不可以包含重复的四元组。

    示例：

    给定数组 nums = [1, 0, -1, 0, -2, 2]，和 target = 0。

    满足要求的四元组集合为：
    [
      [-1,  0, 0, 1],
      [-2, -1, 1, 2],
      [-2,  0, 0, 2]
    ]

题解一|排序+双指针：

和三数之和解法类似，先将数组排序固定两个元素，再用两个指针，一个指向头，一个指向尾，看四数之和为多少，太大了右指针左移，太小了左指针右移，因为有可能存在重复的数组，先将结果保存在set中，最后在转为list输出。

时间复杂度：O(n^3)，空间复杂度O(n)
```
class Solution:
    def fourSum(self, nums: List[int], target: int) -> List[List[int]]:
        nums.sort()
        length=len(nums)
        res=[]
        sets=set()
        
        for i in range(length-3):
            for j in range(i+1,length-2):
                left,right=j+1,length-1
                
                while left<right:
                    sum=nums[i]+nums[j]+nums[left]+nums[right]
                    if sum==target:
                        tmp=(nums[i],nums[j],nums[left],nums[right])
                        sets.add(tmp) # 去重
                        left+=1
                        right-=1
                    elif sum<target:
                        left+=1
                    else:
                        right-=1
                        
        for each in sets:
            res.append(list(each))
        return res 
```
### 26.删除排序数组中的重复项
    链接：https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/

    给定一个排序数组，你需要在原地删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

    不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

    示例 1:

    给定数组 nums = [1,1,2], 

    函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。 

    你不需要考虑数组中超出新长度后面的元素。
    示例 2:

    给定 nums = [0,0,1,1,1,2,2,3,3,4],

    函数应该返回新的长度 5, 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4。

    你不需要考虑数组中超出新长度后面的元素。

题解一|双指针：

思路：

采用两指针，一个指针i指向原数组需要判断的元素，一个指针j指向新数组新加入的元素。  
由于是有序数组，因此只要判断原数组中的元素不同于新数组新加入的元素，就将原数组的元素和新数组后一元素交换位置即可，没有重复元素就是和自身交换位置。

第一个指针 i ：由于数组已完成排序，因为遍历数组，每遇到 nums[i] != nums[j
] ,就说明遇到了新的不同数字，记录之；

第二个指针 j ：每遇到新的不同数字时，执行 j += 1 ， j 指针有两个作用：
    
    记录数组中不同数字的数量；
    作为修改数组元素的索引index。
最终，返回 k+1 即可。
时间复杂度O(n),空间复杂福O(1)。

```
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        if not nums:
            return 0
        j=0
        for i in range(1,len(nums)):
            if nums[i]!=nums[j]:
                nums[j+1]=nums[i]
                j+=1
        return j+1

```

```
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        index=0
        for i in range(1,len(nums)):
            if nums[i] != nums[index]:
                index+=1
                nums[index]=nums[i]
        return index+1

```

```
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        if not nums:
            return 0
        slow=0
        for fast in range(len(nums)):
            if nums[slow] != nums[fast]:
                slow+=1
            nums[slow]=nums[fast]
        return slow+1
```

```
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        if not nums:
            return 0
        slow=0
        for fast in range(len(nums)):
            if nums[fast] == nums[slow]:
                fast+=1
            else:
                slow+=1
                nums[slow]=nums[fast]
        return slow+1
```

```
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        pre,cur=0,1
        while cur<len(nums):
            if nums[pre]==nums[cur]:
                nums.pop(cur)
            else:
                pre,cur=pre+1,cur+1
```
### 27.移除元素
    链接：https://leetcode-cn.com/problems/remove-element/

    给定一个数组 nums 和一个值 val，你需要原地移除所有数值等于 val 的元素，返回移除后数组的新长度。

    不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

    元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

    示例 1:

    给定 nums = [3,2,2,3], val = 3,

    函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。

    你不需要考虑数组中超出新长度后面的元素。
    示例 2:

    给定 nums = [0,1,2,2,3,0,4,2], val = 2,

    函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。

    注意这五个元素可为任意顺序。

    你不需要考虑数组中超出新长度后面的元素。

题解一|双指针：

可以保留两个指针 i 和 j，其中 i 是慢指针，j 是快指针。当 nums[j]与val相等时，递增 j以跳过该元素。只要 nums[j] 不等于 val，我们就复制 nums[j]到 nums[i] 并同时递增两个索引。重复这一过程，直到 j 到达数组的末尾，该数组的新长度为 i。
时间复杂度O(n),空间复杂度O(1).
```
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        i=0
        for j in range(0,len(nums)):
            if nums[j] != val:
                nums[i]=nums[j]
                i+=1
        return i
```

### 29.两数相除
    链接：https://leetcode-cn.com/problems/divide-two-integers/submissions/

    给定两个整数，被除数 dividend 和除数 divisor。将两数相除，要求不使用乘法、除法和 mod 运算符。

    返回被除数 dividend 除以除数 divisor 得到的商。

    示例 1:

    输入: dividend = 10, divisor = 3
    输出: 3
    示例 2:

    输入: dividend = 7, divisor = -3
    输出: -2
    说明:

    被除数和除数均为 32 位有符号整数。
    除数不为 0。
    假设我们的环境只能存储 32 位有符号整数，其数值范围是 [−231,  231 − 1]。本题中，如果除法结果溢出，则返回 231 − 1。

题解一|减法|超时:

超时：当被除数为2147483648，除数为 1，必然超时。

```
class Solution:
    def divide(self, dividend: int, divisor: int) -> int:
        res=0
        flag=1 if dividend ^ divisor >=0 else -1
        dividend=abs(dividend)
        divisor=abs(divisor)
        while dividend >= divisor:
            res+=1
            dividend-=divisor
        res=res*flag
        return min(max(res,-2**31),2**31-1)
```

题解二|增倍除数:
    
    << 是左移，末位补0，类比十进制数在末尾添0相当于原数乘以10，x<<1是将x的二进制表示左移一位，相当于原数x乘2。比如整数4在二进制下是100，4<<1左移1位变成1000(二进制)，结果是8。
    
    >>是右移，右移1位相当于除以2。

```
class Solution:
    def divide(self, dividend: int, divisor: int) -> int:
        res=0
        flag=1 if dividend ^ divisor >=0 else -1
        dividend=abs(dividend)
        divisor=abs(divisor)
        while dividend >= divisor:
            tmp,i=divisor,1
            while dividend>=tmp:
                dividend-=tmp
                res+=i
                i <<=1
                tmp <<=1
        res=res*flag
        return min(max(res,-2**31),2**31-1)
```
题解三（位移）：
算法是把除法化归成移位和减法两种运算方法。对于 10 进制数，移位运算就是乘（左移）除（右移）10，而我们都知道计算机中的移位运算是乘（左移）除（右移）2，因为计算机是通过二进制的方法存储数的。这样，类比十进制，二进制的除法（仍以 45/2 为例）可以写作（注意，这里我们并没有用到乘除法）

```
class Solution:
    def divide(self, dividend: int, divisor: int) -> int:
        res=0
        flag=1 if dividend ^ divisor >=0 else -1
        dividend=abs(dividend)
        divisor=abs(divisor)
        count=0

        # 不断左移，直到大于被除数
        while dividend >= divisor:
            count+=1
            divisor <<=1
        
        while count > 0:
            count -=1
            divisor >>=1
            res <<=1
            if divisor <= dividend:
                res+=1
                dividend -= divisor
        
        res=res*flag
        return min(max(res,-2**31),2**31-1)
```

### 31. 下一个排列
    链接：https://leetcode-cn.com/problems/next-permutation/

    实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。

    如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

    必须原地修改，只允许使用额外常数空间。

    以下是一些例子，输入位于左侧列，其相应输出位于右侧列。
    1,2,3 → 1,3,2
    3,2,1 → 1,2,3
    1,1,5 → 1,5,1

题解一：

1.从右至左遍历nums，发现第一个小于右边的数nums[i]，将该nums[i]之后的数排升序；

2.第二层遍历nums[(i+1):]，发现第一个大于nums[i]的数nums[j]，交换两数，退出遍历break；

3.交换了也排好序了，退出第一次层遍历return nums

这里有个细节：nums本来就是降序，第一次遍历找不到nums[i],那么直接sort()

```
class Solution:
    def nextPermutation(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        for i in range(len(nums)-2,-1,-1):
            if nums[i] < nums[i+1]:
                nums[i+1:]=sorted(nums[i+1:])
                for j in range(i+1,len(nums)):
                    if nums[i] < nums[j]:
                        nums[i],nums[j]=nums[j],nums[i]
                        # break
                        return

        nums.sort()
```


### 36. 有效的数独
    链接：https://leetcode-cn.com/problems/valid-sudoku/

    判断一个 9x9 的数独是否有效。只需要根据以下规则，验证已经填入的数字是否有效即可。

    数字 1-9 在每一行只能出现一次。
    数字 1-9 在每一列只能出现一次。
    数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。


    上图是一个部分填充的有效的数独。

    数独部分空格内已填入了数字，空白格用 '.' 表示。

    示例 1:

    输入:
    [
      ["5","3",".",".","7",".",".",".","."],
      ["6",".",".","1","9","5",".",".","."],
      [".","9","8",".",".",".",".","6","."],
      ["8",".",".",".","6",".",".",".","3"],
      ["4",".",".","8",".","3",".",".","1"],
      ["7",".",".",".","2",".",".",".","6"],
      [".","6",".",".",".",".","2","8","."],
      [".",".",".","4","1","9",".",".","5"],
      [".",".",".",".","8",".",".","7","9"]
    ]
    输出: true
    示例 2:

    输入:
    [
      ["8","3",".",".","7",".",".",".","."],
      ["6",".",".","1","9","5",".",".","."],
      [".","9","8",".",".",".",".","6","."],
      ["8",".",".",".","6",".",".",".","3"],
      ["4",".",".","8",".","3",".",".","1"],
      ["7",".",".",".","2",".",".",".","6"],
      [".","6",".",".",".",".","2","8","."],
      [".",".",".","4","1","9",".",".","5"],
      [".",".",".",".","8",".",".","7","9"]
    ]
    输出: false
    解释: 除了第一行的第一个数字从 5 改为 8 以外，空格内其他数字均与 示例1 相同。
         但由于位于左上角的 3x3 宫内有两个 8 存在, 因此这个数独是无效的。
    说明:

    一个有效的数独（部分已被填充）不一定是可解的。
    只需要根据以上规则，验证已经填入的数字是否有效即可。
    给定数独序列只包含数字 1-9 和字符 '.' 。
    给定数独永远是 9x9 形式的。

题解一：
```
class Solution:
    def isValidSudoku(self, board: List[List[str]]) -> bool:
        rows=[{} for i in range(9)]
        columns=[{} for i in range(9)]
        boxes=[{} for i in range(9)]
        for i in range(9):
            for j in range(9):
                char=board[i][j]
                if char != '.':
                    index=(i//3)*3+j//3
                    rows[i][char]=rows[i].get(char,0)+1
                    columns[j][char]=columns[j].get(char,0)+1
                    boxes[index][char]=boxes[index].get(char,0)+1

                    if rows[i][char]>1 or columns[j][char]>1 or boxes[index][char]>1:
                        return False
        return True
```

### 45. 跳跃游戏 II
    链接：https://leetcode-cn.com/problems/jump-game-ii/

    给定一个非负整数数组，你最初位于数组的第一个位置。

    数组中的每个元素代表你在该位置可以跳跃的最大长度。

    你的目标是使用最少的跳跃次数到达数组的最后一个位置。

    示例:

    输入: [2,3,1,1,4]
    输出: 2
    解释: 跳到最后一个位置的最小跳跃数是 2。
         从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。

题解一：
思路：
    
    1、如果某一个作为 起跳点 的格子可以跳跃的距离是 3，那么表示后面 3 个格子都可以作为 起跳点。
        11. 可以对每一个能作为 起跳点 的格子都尝试跳一次，把 能跳到最远的距离 不断更新。

    2、如果从这个 起跳点 起跳叫做第 1 次 跳跃，那么从后面 3 个格子起跳 都 可以叫做第 2 次 跳跃。

    3、所以，当一次 跳跃 结束时，从下一个格子开始，到现在 能跳到最远的距离，都 是下一次 跳跃 的 起跳点。
        31. 对每一次 跳跃 用 for 循环来模拟。
        32. 跳完一次之后，更新下一次 起跳点 的范围。
        33. 在新的范围内跳，更新 能跳到最远的距离。

    4、记录 跳跃 次数，如果跳到了终点，就得到了结果。

```
class Solution:
    def jump(self, nums: List[int]) -> int:
        ans=0
        start=0
        end=1
        while end<len(nums):
            maxPos=0
            for i in range(start,end):
                maxPos=max(maxPos,nums[i]+i)
            start=end
            end=maxPos+1
            ans+=1
        return ans
```

优化：
```
class Solution:
    def jump(self, nums: List[int]) -> int:
        ans=0
        end=0
        maxPos=0
        for i in range(0,len(nums)-1):
            maxPos=max(maxPos,nums[i]+i)
            if i==end:
                end=maxPos
                ans+=1
        return ans
```

### 48.旋转图像
    链接：https://leetcode-cn.com/problems/rotate-image/

    给定一个 n × n 的二维矩阵表示一个图像。

    将图像顺时针旋转 90 度。

    说明：

    你必须在原地旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要使用另一个矩阵来旋转图像。

    示例 1:

    给定 matrix = 
    [
      [1,2,3],
      [4,5,6],
      [7,8,9]
    ],

    原地旋转输入矩阵，使其变为:
    [
      [7,4,1],
      [8,5,2],
      [9,6,3]
    ]
    示例 2:

    给定 matrix =
    [
      [ 5, 1, 9,11],
      [ 2, 4, 8,10],
      [13, 3, 6, 7],
      [15,14,12,16]
    ], 

    原地旋转输入矩阵，使其变为:
    [
      [15,13, 2, 5],
      [14, 3, 4, 1],
      [12, 6, 8, 9],
      [16, 7,10,11]
    ]

题解一|转置+翻转：

分析：先转置矩阵，然后翻转每一行。这个简单的方法已经能达到最优的时间复杂度O(N^2)。

时间复杂度：O(N^2)
空间复杂度：O(1) 由于旋转操作是 就地 完成的。

```
class Solution:
    def rotate(self, matrix: List[List[int]]) -> None:
        """
        Do not return anything, modify matrix in-place instead.
        """
        n=len(matrix[0]) # 矩阵第一行matrix[0]
        # 矩阵转置
        for i in range(n):
            for j in range(i,n):
                matrix[j][i],matrix[i][j]=matrix[i][j],matrix[j][i]
        # 翻转每行
        for i in range(n):
            matrix[i].reverse()
        return matrix
```

参考：https://leetcode-cn.com/problems/rotate-image/solution/xuan-zhuan-tu-xiang-by-leetcode/

### 49. 字母异位词分组
    链接：https://leetcode-cn.com/problems/group-anagrams/

    给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

    示例:

    输入: ["eat", "tea", "tan", "ate", "nat", "bat"],
    输出:
    [
      ["ate","eat","tea"],
      ["nat","tan"],
      ["bat"]
    ]
    说明：

    所有输入均为小写字母。
    不考虑答案输出的顺序。

题解一（hash）：

```
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        hash={}
        for i in strs:
            tmp=tuple(sorted(i))
            if tmp not in hash:
                hash[tmp]=[i]
            else:
                hash[tmp].append(i)
        # print(hash)
        res=[i for i in hash.values()]
        return res
```

输出：

    {('a', 'e', 't'): ['eat', 'tea', 'ate'], ('a', 'n', 't'): ['tan', 'nat'], ('a', 'b', 't'): ['bat']}


### 66. 加一

链接：https://leetcode-cn.com/problems/plus-one/

    给定一个由整数组成的非空数组所表示的非负整数，在该数的基础上加一。

    最高位数字存放在数组的首位， 数组中每个元素只存储单个数字。

    你可以假设除了整数 0 之外，这个整数不会以零开头。

    示例 1:

    输入: [1,2,3]
    输出: [1,2,4]
    解释: 输入数组表示数字 123。
    示例 2:

    输入: [4,3,2,1]
    输出: [4,3,2,2]
    解释: 输入数组表示数字 4321。

题解一：

思路：转换为int进行加1

题解二：
```
class Solution:
    def plusOne(self, digits: List[int]) -> List[int]:
        for i in range(len(digits)-1,-1,-1):
            digits[i]+=1
            digits[i] %= 10
            if digits[i] != 0:
                return digits
        
        digits=[0]*(len(digits)+1) # 解决case[9],输出[1,0]
        digits[0]=1 # 解决case[9],输出[1,0]
        return digits    
```

题解三：
```
class Solution:
    def plusOne(self, digits: List[int]) -> List[int]:
        new=[]
        while digits and digits[-1] == 9:
            digits.pop()
            new.append(0)
        if not digits:
            return [1]+new
        else:
            digits[-1]+=1
            return digits+new
```

题解四：
```
class Solution:
    def plusOne(self, digits):
        length=len(digits)
        i=length-1
        while(i>=0):
            if i==0:
                if (digits[i]+1)>9:
                    digits[i]=0
                    result=[1]
                    result.extend(digits)
                    return result
            if (digits[i]+1)<=9:
                digits[i]+=1
                return digits
            else:
                digits[i]=0
                i-=1
        return digits
```
### 69.x的平方根
    链接：https://leetcode-cn.com/problems/sqrtx/
    
    实现 int sqrt(int x) 函数。

    计算并返回 x 的平方根，其中 x 是非负整数。

    由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

    示例 1:

    输入: 4
    输出: 2
    示例 2:

    输入: 8
    输出: 2
    说明: 8 的平方根是 2.82842..., 
         由于返回类型是整数，小数部分将被舍去。

题解一(二分法):

思路分析：使用二分法搜索平方根的思想很简单，就类似于小时候我们看的电视节目中的“猜价格”游戏，高了就往低了猜，低了就往高了猜，范围越来越小。因此，使用二分法猜算术平方根就很自然。

一个数的平方根肯定不会超过它自己，不过直觉还告诉我们，一个数的平方根最多不会超过它的一半，例如 8的平方根，8 的一半是 4，4^2=16>8，如果这个数越大越是如此，因此我们要计算一下，这个边界是多少。为此，解如下不等式：(a/2)^2 >= a

时间复杂度：O(logn)
空间复杂度：O(1)

```
class Solution:
    def mySqrt(self, x: int) -> int:
        left=0
        right=x//2+1
        while left < right:
            # mid = left + (right - left + 1) // 2
            # mid=(left+right+1)>>1
            mid=(left+right+1)//2 # 使用除法的耗时比移位多
            square=mid*mid

            if square>x:
                right=mid-1
            else:
                left=mid
            
        return left
```

```
# 单独照顾0这个特例

class Solution:
    def mySqrt(self, x: int) -> int:
        if x==0:
            return 0
        left=1
        right=x//2
        while left < right:
            # mid=(left+right+1)>>1
            mid=(left+right+1)//2 # 使用除法的耗时比移位多
            # 一定取右中位数，如果取左中位数(left+right)//2，代码可能会进入死循环
            square=mid*mid

            if square>x:
                right=mid-1
            else:
                left=mid
            
        return left
```

```
class Solution:

    def mySqrt(self, x):
        left = 0
        right = 999999
        while left < right:
            # 这种取中位数的方法又快又好，是我刚学会的，原因在下面这篇文章的评论区
            # https://www.liwei.party/2019/06/17/leetcode-solution-new/search-insert-position/
            mid = (left + right + 1) >> 1
            square = mid * mid
            if square > x:
                right = mid - 1
            else:
                left = mid

        return left
```
?题解二（牛顿法）:

牛顿法的应用：一个是求方程的根，另一个是求解最优化问题

使用牛顿法可以得到一个正实数的算术平方根，因为题目中说“结果只保留整数部分”，因此，我们把使用牛顿法得到的浮点数转换为整数即可。

在迭代过程中，以直线代替曲线，用一阶泰勒展式（即在当前点的切线）代替原曲线，求直线与 xx 轴的交点，重复这个过程直到收敛。
![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/newton.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/newton.png)

```
class Solution:
    def mySqrt(self, x: int) -> int:
        if x<0:
            # raise Exception('不能输入负数)
            return -1
        if x==0:
            return 0
        
        cur=1
        while True:
            pre=cur
            cur=(cur+x/cur)/2
            if abs(cur-pre)< 1e-6:
                return int(cur)
```

### 73. 矩阵置零
    链接：https://leetcode-cn.com/problems/set-matrix-zeroes/

    给定一个 m x n 的矩阵，如果一个元素为 0，则将其所在行和列的所有元素都设为 0。请使用原地算法。

    示例 1:

    输入: 
    [
      [1,1,1],
      [1,0,1],
      [1,1,1]
    ]
    输出: 
    [
      [1,0,1],
      [0,0,0],
      [1,0,1]
    ]
    示例 2:

    输入: 
    [
      [0,1,2,0],
      [3,4,5,2],
      [1,3,1,5]
    ]
    输出: 
    [
      [0,0,0,0],
      [0,4,5,0],
      [0,3,1,0]
    ]
    进阶:

    一个直接的解决方案是使用  O(mn) 的额外空间，但这并不是一个好的解决方案。
    一个简单的改进方案是使用 O(m + n) 的额外空间，但这仍然不是最好的解决方案。
    你能想出一个常数空间的解决方案吗？

题解一：

分析：
    
    用 O(m+n)额外空间
    两遍扫matrix,第一遍用集合记录哪些行,哪些列有0;第二遍置0

时间复杂度：O(M*N)
空间复杂度：O(M+N)

```
class Solution:
    def setZeroes(self, matrix: List[List[int]]) -> None:
        """
        Do not return anything, modify matrix in-place instead.
        """
        m=len(matrix)       # 行
        n=len(matrix[0])    # 列
        m_zero=set()
        n_zero=set()
        for i in range(m):
            for j in range(n):
                if matrix[i][j]== 0:
                    m_zero.add(i)
                    n_zero.add(j)
        for i in range(m):
            for j in range(n):
                if i in m_zero or j in n_zero: # 不是if 0 in m_zero or 0 in n_zero
                    matrix[i][j]=0
        return matrix
```

题解二：

分析:

    用matrix第一行和第一列记录该行该列是否有0,作为标志位
    但是对于第一行,和第一列要设置一个标志位,为了防止自己这一行(一列)也有0的情况.


    参考：https://leetcode-cn.com/problems/set-matrix-zeroes/solution/li-yong-di-yi-xing-he-di-yi-lie-ji-lu-o1kong-jian-/

    定义flag_line=False，判断第一行是否需要置0。
    定义flag_row=False，判断第一列是否需要置0。
    遍历第一列，若第一列中存在0，将flag_row=True，表示需要将第一列置0。
    遍历第一行，若第一行中存在0，将flag_line=True，表示需要将第一行置0。
    遍历矩阵，遍历区间，行区间[1,m)，列区间[1,m)，若matrix[i][j]==0，则将对应的行和列记录下来，即将第一行和第一列中对应的位置置为0。matrix[i][0]=matrix[0][j]=0
    再遍历一次矩阵，若当前位置的行或列索引对应的第一行或者第一列处为0，即matrix[i][0]==0 or matrix[0][j]==0，将此位置置为0。
    根据flag_line，判断是否需要将第一行置为0。
    根据flag_row，判断第一列是否需要置0。


    时间复杂度：O(M*N)
    空间复杂度：O(1)

```
class Solution:
    def setZeroes(self, matrix: List[List[int]]) -> None:
        """
        Do not return anything, modify matrix in-place instead.
        """
        # 输入：[[1,1,1,1],[1,0,1,1],[1,1,1,1]]
        m=len(matrix)       # 行的个数
        n=len(matrix[0])    # 列的个数
        m0_flag=False
        n0_flag=False
        
        # 找第一行是否有0
        for j in range(n):
            if matrix[0][j] == 0:
                m0_flag=True
                break
        # 找第一列是否有0
        for i in range(m):
            if matrix[i][0]==0:
                n0_flag=True
                break
        # 把第一行或者第一列作为 标志位
        for i in range(1,m):
            for j in range(1,n):
                if matrix[i][j]==0:
                    matrix[i][0]=matrix[0][j]=0
        # print(matrix)
        # [[1, 0, 1, 1], [0, 0, 1, 1], [1, 1, 1, 1]]

        # 置0
        for i in range(1,m):
            for j in range(1,n):
                if(matrix[i][0]==0 or matrix[0][j]==0):
                    matrix[i][j]=0    
        # print(matrix)  
        # [[1, 0, 1, 1], [0, 0, 0, 0], [1, 0, 1, 1]]
        
        if m0_flag:
            for j in range(n):
                matrix[0][j]=0
        if n0_flag:
            for i in range(m):
                matrix[i][0]=0
```

???
```
class Solution:
    def setZeroes(self, matrix: List[List[int]]) -> None:
        """
        Do not return anything, modify matrix in-place instead.
        """
        flag_col = False
        row = len(matrix)
        col = len(matrix[0])
        for i in range(row):
            if matrix[i][0] == 0: flag_col = True
            for j in range(1,col):
                if matrix[i][j] == 0:
                    matrix[i][0] = matrix[0][j] = 0
        
        for i in range(row - 1, -1, -1):
            for j in range(col - 1, 0, -1):
                if matrix[i][0] == 0 or matrix[0][j] == 0:
                    matrix[i][j] = 0

            if flag_col == True: matrix[i][0] = 0

```

### 88. 合并两个有序数组

    链接：https://leetcode-cn.com/problems/merge-sorted-array/

    给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 num1 成为一个有序数组。

    说明:

    初始化 nums1 和 nums2 的元素数量分别为 m 和 n 。
    你可以假设 nums1 有足够的空间（空间大小大于或等于 m + n）来保存 nums2 中的元素。
     

    示例:

    输入:
    nums1 = [1,2,3,0,0,0], m = 3
    nums2 = [2,5,6],       n = 3

    输出: [1,2,2,3,5,6]

题解一（合并后再排序）：

时间复杂度 : O((n + m)\log(n + m))
空间复杂度 : O(1)
```
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        nums1[:]=sorted(nums1[:m]+nums2)
```

题解二（双指针 / 从前往后）：

时间复杂度 : O(n + m)
空间复杂度 : O(m)
```
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        copy=nums1[:m]
        nums1[:]=[]
        p1,p2=0,0
        while p1<m and p2<n:
            if copy[p1]<nums2[p2]:
                nums1.append(copy[p1])
                p1+=1
            else:
                nums1.append(nums2[p2])
                p2+=1
        if p1<m:
            nums1[p1+p2:]=copy[p1:]
        if p2<n:
            nums1[p1+p2:]=nums2[p2:]
```

题解三(双指针 / 从后往前)：

时间复杂度 : O(n + m)
空间复杂度 : O(1)

```
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        p1=m-1
        p2=n-1
        p=m+n-1
        while p1>=0 and p2>=0:
            if nums1[p1] < nums2[p2]:
                nums1[p]=nums2[p2]
                p2-=1
            else:
                nums1[p]=nums1[p1]
                p1-=1
            p-=1
        nums1[:p2+1]=nums2[:p2+1] # 解决case:[0]，0，[1]，1
```

### 167. 两数之和 II - 输入有序数组
    链接：https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/

    给定一个已按照升序排列 的有序数组，找到两个数使得它们相加之和等于目标数。

    函数应该返回这两个下标值 index1 和 index2，其中 index1 必须小于 index2。

    说明:

    返回的下标值（index1 和 index2）不是从零开始的。
    你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。
    示例:

    输入: numbers = [2, 7, 11, 15], target = 9
    输出: [1,2]
    解释: 2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。

题解一|暴力|超时：

```
class Solution:
    def twoSum(self, numbers: List[int], target: int) -> List[int]:
        n=len(numbers)
        for i in range(n):
            for j in range(i+1,n):
                if numbers[i]+numbers[j]==target:
                    return [i+1,j+1]
```

题解二|双指针:

时间复杂度：O(n)
空间复杂度：O(1)
```
class Solution:
    def twoSum(self, numbers: List[int], target: int) -> List[int]:
        left=0
        right=len(numbers)-1
        while left < right:
            if numbers[left]+numbers[right]==target:
                return [left+1,right+1]
            elif numbers[left]+numbers[right] > target:
                right-=1
            else:
                left+=1
        return False
```

### 189. 旋转数组
    链接：https://leetcode-cn.com/problems/rotate-array/

    给定一个数组，将数组中的元素向右移动 k 个位置，其中 k 是非负数。

    示例 1:

    输入: [1,2,3,4,5,6,7] 和 k = 3
    输出: [5,6,7,1,2,3,4]
    解释:
    向右旋转 1 步: [7,1,2,3,4,5,6]
    向右旋转 2 步: [6,7,1,2,3,4,5]
    向右旋转 3 步: [5,6,7,1,2,3,4]
    示例 2:

    输入: [-1,-100,3,99] 和 k = 2
    输出: [3,99,-1,-100]
    解释: 
    向右旋转 1 步: [99,-1,-100,3]
    向右旋转 2 步: [3,99,-1,-100]
    说明:

    尽可能想出更多的解决方案，至少有三种不同的方法可以解决这个问题。
    要求使用空间复杂度为 O(1) 的 原地 算法。

题解一|暴力|超时：

时间复杂度：O(n^2)
空间复杂度：O(1)
```
class Solution:
    def rotate(self, nums: List[int], k: int) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        if not nums:
            return ''
        n=len(nums)
        k=k%n
        while k!=0:
            tmp=nums[n-1]
            i=n-1
            while i>0:
                nums[i]=nums[i-1]
                i-=1
            nums[0]=tmp
            k-=1
        return nums
```

题解二|切片：

```
class Solution:
    def rotate(self, nums: List[int], k: int) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        n=len(nums)
        if n < 2:
            pass
        else:
            while k>0:
                tmp=nums[-1]
                nums[1:n]=nums[0:n-1]
                nums[0]=tmp
                k-=1
```

### 283. 移动零
    链接：https://leetcode-cn.com/problems/move-zeroes/

    给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

    示例:

    输入: [0,1,0,3,12]
    输出: [1,3,12,0,0]
    说明:

    必须在原数组上操作，不能拷贝额外的数组。
    尽量减少操作次数。

思路：
    循环，统计0的个数，非0元素前移，0放在最后；

    新开一个数组，碰到0后放，非0放在前面；

    index记录非0的位置；

题解一：
```
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        index=0
        for i in range(len(nums)):
            if nums[i] != 0:
                nums[index]=nums[i]
                index+=1
        for i in range(index,len(nums)):
            nums[i]=0
        return nums
```

```
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        index=0
        for i in range(len(nums)):
            if nums[i] != 0:
                nums[index]=nums[i]
                if i != index:
                    nums[i]=0
                index+=1
        return nums
```

### 442. 数组中重复的数据
    链接：https://leetcode-cn.com/problems/find-all-duplicates-in-an-array/

    给定一个整数数组 a，其中1 ≤ a[i] ≤ n （n为数组长度）, 其中有些元素出现两次而其他元素出现一次。

    找到所有出现两次的元素。

    你可以不用到任何额外空间并在O(n)时间复杂度内解决这个问题吗？

    示例：

    输入:
    [4,3,2,7,8,2,3,1]

    输出:
    [2,3]

题解一|原地变负：

注意：1 ≤ a[i] ≤ n，所以变负时才不会越界。

```
class Solution:
    def findDuplicates(self, nums: List[int]) -> List[int]:
        ans=[]
        for num in nums:
            if nums[abs(num)-1] < 0:
                ans.append(abs(num))
            nums[abs(num)-1]*=-1
        return ans
```

### 498. 对角线遍历
    给定一个含有 M x N 个元素的矩阵（M 行，N 列），请以对角线遍历的顺序返回这个矩阵中的所有元素，对角线遍历如下图所示。

    示例:

    输入:
    [
     [ 1, 2, 3 ],
     [ 4, 5, 6 ],
     [ 7, 8, 9 ]
    ]

    输出:  [1,2,4,7,5,3,6,8,9]

    解释:

    说明:

    给定矩阵中的元素总数不会超过 100000 。

题解一：

规律：

    对于矩形来说，矩形对角线是最长的，也是遍历结果最长的一条，从这条线，到两边对称递减

    遍历顺序：从左下角到右上角交错进行

根据以上两个特点，我们可以想到是否同一条线上的坐标有数上的关系，而且，交替遍历是否可以看成奇数和偶数的替换呢？带着这种思考我们解析这个矩阵(以图中矩阵为例子)：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/duijiaoxian.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/duijiaoxian.png)

    1、我们现在将每一条对角线作出从0 开始的编号，可以发现最后一个的编号是 M + N - 1；并且如果编号为奇数自上而下遍历，偶数则是自下而上遍历

    2、关注每一条对角线上的坐标和：对于每一条对角线上的坐标(m,n)，m + n = 编号 是我们发现的规律

    3、最后一步就是确定边界：

        多少次遍历：矩阵有多少个元素就遍历多少次；
        到什么时候结束：对于最长对角线以上来说，到 m 或者 n 中的某一个到 0 为止；对于最长对角线下面来说，到 m 或者 n 中的某一个到达边界为止。


```
class Solution:
    def findDiagonalOrder(self, matrix: List[List[int]]) -> List[int]:
        if not matrix:
            return []
        size=len(matrix)*len(matrix[0])
        index=0
        result=[0]*size
        maxK=len(matrix)+len(matrix[0])


        for k in range(maxK):
            m,n=0,0
            if k%2==0: # 偶数部分
                if k<len(matrix):
                    m=k
                    n=0
                else:
                    m=len(matrix)-1
                    n=k-m
                while m>=0 and n<len(matrix[0]): # n到达边界为止
                    result[index]=matrix[m][n]
                    index+=1
                    m-=1
                    n+=1
            else: # 奇数部分
                if k<len(matrix[0]):
                    m=0
                    n=k
                else:
                    n=len(matrix[0])-1
                    m=k-n
                while m<len(matrix) and n>=0: # m到达边界为止
                    result[index]=matrix[m][n]
                    index+=1
                    m+=1
                    n-=1
        return result
```

### 1424. 对角线遍历 II
    链接：https://leetcode-cn.com/problems/diagonal-traverse-ii/solution/mei-ge-shu-zu-yuan-su-de-wei-zhi-jue-ding-liao-ta-/
    
    给你一个列表 nums ，里面每一个元素都是一个整数列表。请你依照下面各图的规则，按顺序返回 nums 中对角线上的整数。

    示例 1：

    输入：nums = [[1,2,3],[4,5,6],[7,8,9]]
    输出：[1,4,2,7,5,3,8,6,9]
    示例 2：

    输入：nums = [[1,2,3,4,5],[6,7],[8],[9,10,11],[12,13,14,15,16]]
    输出：[1,6,2,8,7,3,9,4,12,10,5,13,11,14,15,16]
    示例 3：

    输入：nums = [[1,2,3],[4],[5,6,7],[8],[9,10,11]]
    输出：[1,4,2,5,3,8,6,9,7,10,11]
    示例 4：

    输入：nums = [[1,2,3,4,5,6]]
    输出：[1,2,3,4,5,6]
     
    提示：

    1 <= nums.length <= 10^5
    1 <= nums[i].length <= 10^5
    1 <= nums[i][j] <= 10^9
    nums 中最多有 10^5 个数字。

```
class Solution:
    def findDiagonalOrder(self, nums: List[List[int]]) -> List[int]:
        sub=[]
        for i in range(len(nums)):
            for j in range(len(nums[i])):
                if i+j >= len(sub):
                    sub.append([])
                sub[i+j].append(nums[i][j])
        result=[]
        for i in sub:
            result+=i[::-1]
        return result
```
