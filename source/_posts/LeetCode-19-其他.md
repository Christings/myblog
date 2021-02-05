---
title: LeetCode-19-其他
date: 2020-11-02 12:17:10
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
### 190. 颠倒二进制位
    链接：https://leetcode-cn.com/problems/reverse-bits/

    颠倒给定的 32 位无符号整数的二进制位。

    示例 1：

    输入: 00000010100101000001111010011100
    输出: 00111001011110000010100101000000
    解释: 输入的二进制串 00000010100101000001111010011100 表示无符号整数 43261596，
         因此返回 964176192，其二进制表示形式为 00111001011110000010100101000000。
    示例 2：

    输入：11111111111111111111111111111101
    输出：10111111111111111111111111111111
    解释：输入的二进制串 11111111111111111111111111111101 表示无符号整数 4294967293，
         因此返回 3221225471 其二进制表示形式为 10111111111111111111111111111111 。
     

    提示：

    请注意，在某些语言（如 Java）中，没有无符号整数类型。在这种情况下，输入和输出都将被指定为有符号整数类型，并且不应影响您的实现，因为无论整数是有符号的还是无符号的，其内部的二进制表示形式都是相同的。
    在 Java 中，编译器使用二进制补码记法来表示有符号整数。因此，在上面的 示例 2 中，输入表示有符号整数 -3，输出表示有符号整数 -1073741825。
     

    进阶:
    如果多次调用这个函数，你将如何优化你的算法？

题解一：

zfill():32位右对齐，不够的话，左边补0

```
class Solution:
    def reverseBits(self, n: int) -> int:
        return int(bin(n)[2:].zfill(32)[::-1],2)
```

题解二|移位：
```
class Solution:
    def reverseBits(self, n: int) -> int:
        res=0
        count=32
        while count:
            res<<=1
            res+=n&1
            n>>=1
            count-=1
        return int(bin(res),2)
```

### 191. 位1的个数
    链接：https://leetcode-cn.com/problems/number-of-1-bits/

    编写一个函数，输入是一个无符号整数，返回其二进制表达式中数字位数为 ‘1’ 的个数（也被称为汉明重量）。

    示例 1：

    输入：00000000000000000000000000001011
    输出：3
    解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。
    示例 2：

    输入：00000000000000000000000010000000
    输出：1
    解释：输入的二进制串 00000000000000000000000010000000 中，共有一位为 '1'。
    示例 3：

    输入：11111111111111111111111111111101
    输出：31
    解释：输入的二进制串 11111111111111111111111111111101 中，共有 31 位为 '1'。
     

    提示：

    请注意，在某些语言（如 Java）中，没有无符号整数类型。在这种情况下，输入和输出都将被指定为有符号整数类型，并且不应影响您的实现，因为无论整数是有符号的还是无符号的，其内部的二进制表示形式都是相同的。
    在 Java 中，编译器使用二进制补码记法来表示有符号整数。因此，在上面的 示例 3 中，输入表示有符号整数 -3。
     

    进阶:
    如果多次调用这个函数，你将如何优化你的算法？

题解一|循环：
```
class Solution:
    def hammingWeight(self, n: int) -> int:
        tmp=bin(n)[2:]
        res=0
        for i in tmp:
            if int(i)==1:
                res+=1
        return res    
```

题解二|循环和位移动：
```
class Solution:
    def hammingWeight(self, n: int) -> int:
        bits=0
        mask=1
        for i in range(32):
            if (n & mask) != 0:
                bits+=1
            mask<<=1
        return bits
```

题解三|位操作：

时间复杂度：O(1) 。运行时间与 n 中位为 1 的有关。在最坏情况下， n 中所有位都是 11 。对于 32 位整数，运行时间是 O(1) 的。

空间复杂度：O(1) ，没有使用额外空间。

```
class Solution:
    def hammingWeight(self, n: int) -> int:
        res=0
        while n != 0:
            res+=1
            n &= (n-1)
        return res
```

### 268. 丢失的数字
    链接：https://leetcode-cn.com/problems/missing-number/

    给定一个包含 [0, n] 中 n 个数的数组 nums ，找出 [0, n] 这个范围内没有出现在数组中的那个数。

    进阶：

    你能否实现线性时间复杂度、仅使用额外常数空间的算法解决此问题?
     

    示例 1：

    输入：nums = [3,0,1]
    输出：2
    解释：n = 3，因为有 3 个数字，所以所有的数字都在范围 [0,3] 内。2 是丢失的数字，因为它没有出现在 nums 中。
    示例 2：

    输入：nums = [0,1]
    输出：2
    解释：n = 2，因为有 2 个数字，所以所有的数字都在范围 [0,2] 内。2 是丢失的数字，因为它没有出现在 nums 中。
    示例 3：

    输入：nums = [9,6,4,2,3,5,7,0,1]
    输出：8
    解释：n = 9，因为有 9 个数字，所以所有的数字都在范围 [0,9] 内。8 是丢失的数字，因为它没有出现在 nums 中。
    示例 4：

    输入：nums = [0]
    输出：1
    解释：n = 1，因为有 1 个数字，所以所有的数字都在范围 [0,1] 内。1 是丢失的数字，因为它没有出现在 nums 中。
 
    提示：

    n == nums.length
    1 <= n <= 104
    0 <= nums[i] <= n
    nums 中的所有数字都 独一无二

题解一|数学：
```
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        n=len(nums)
        ans=((n+1)*n)//2
        for i in range(len(nums)):
            ans-=nums[i]
        return ans
```

### 371. 两整数之和
    链接：https://leetcode-cn.com/problems/sum-of-two-integers/

    不使用运算符 + 和 - ​​​​​​​，计算两整数 ​​​​​​​a 、b ​​​​​​​之和。

    示例 1:

    输入: a = 1, b = 2
    输出: 3
    示例 2:

    输入: a = -2, b = 3
    输出: 1

题解一|位运算：

参考：https://leetcode-cn.com/problems/sum-of-two-integers/solution/wei-yun-suan-xiang-jie-yi-ji-zai-python-zhong-xu-y/

```
class Solution:
    def getSum(self, a: int, b: int) -> int:
        # 2^32
        MASK = 0x100000000
        # 整型最大值
        MAX_INT = 0x7FFFFFFF
        MIN_INT = MAX_INT + 1
        while b != 0:
            # 计算进位
            carry = (a & b) << 1 
            # 取余范围限制在 [0, 2^32-1] 范围内
            a = (a ^ b) % MASK
            b = carry % MASK
        return a if a <= MAX_INT else ~((a % MIN_INT) ^ MAX_INT) 

```

### 461. 汉明距离
    链接：https://leetcode-cn.com/problems/hamming-distance/

    两个整数之间的汉明距离指的是这两个数字对应二进制位不同的位置的数目。

    给出两个整数 x 和 y，计算它们之间的汉明距离。

    注意：
    0 ≤ x, y < 231.

    示例:

    输入: x = 1, y = 4

    输出: 2

    解释:
    1   (0 0 0 1)
    4   (0 1 0 0)
           ↑   ↑

    上面的箭头指出了对应二进制位不同的位置。

题解一|内置函数：

时间复杂度：O(1)
空间复杂度：O(1)

```
class Solution:
    def hammingDistance(self, x: int, y: int) -> int:
        return bin(x^y).count('1')
```

题解二|移位：

时间复杂度：O(1)
空间复杂度：O(1)

```
class Solution:
    def hammingDistance(self, x: int, y: int) -> int:
        xor=x^y
        count=0
        while xor:
            if xor & 1:
                count+=1
            xor=xor>>1
        return count
```
