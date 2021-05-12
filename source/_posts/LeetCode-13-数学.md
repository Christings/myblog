---
title: LeetCode-13-数学
date: 2020-03-29 20:12:16
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
## 十三、数学

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

题解三|位移：
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

### 50. Pow(x, n)
    链接：https://leetcode-cn.com/problems/powx-n/

    实现 pow(x, n) ，即计算 x 的 n 次幂函数。

    示例 1:

    输入: 2.00000, 10
    输出: 1024.00000
    示例 2:

    输入: 2.10000, 3
    输出: 9.26100
    示例 3:

    输入: 2.00000, -2
    输出: 0.25000
    解释: 2-2 = 1/22 = 1/4 = 0.25
    说明:

    -100.0 < x < 100.0
    n 是 32 位有符号整数，其数值范围是 [−231, 231 − 1] 。

参考：https://leetcode-cn.com/problems/powx-n/solution/powx-n-by-leetcode-solution/

题解一|快速幂|递归：

时间复杂度：O(logn)，即为递归的层数。
空间复杂度：O(logn)，即为递归的层数。这是由于递归的函数调用会使用栈空间。

```
class Solution(object):
    def myPow(self, x, n):
        """
        :type x: float
        :type n: int
        :rtype: float
        """
        def helper(n):
            if n==0:
                return 1
            y=helper(n//2)
            return y*y if n%2==0 else y*y*x
        return helper(n) if n>=0 else 1/helper(-n)

```

题解二|快速幂|迭代：

时间复杂度：O(logn)
空间复杂度：O(1)

```
class Solution(object):
    def myPow(self, x, n):
        """
        :type x: float
        :type n: int
        :rtype: float
        """
        def helper(n):
            ans=1
            new=x
            while n>0:
                if n%2==1:
                    ans*=new
                new*=new
                n//=2
            return ans
                
        return helper(n) if n>=0 else 1/helper(-n)
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

题解一|二分法:

思路分析：使用二分法搜索平方根的思想很简单，就类似于小时候我们看的电视节目中的“猜价格”游戏，高了就往低了猜，低了就往高了猜，范围越来越小。因此，使用二分法猜算术平方根就很自然。

一个数的平方根肯定不会超过它自己，不过直觉还告诉我们，一个数的平方根最多不会超过它的一半，例如 8的平方根，8 的一半是 4，4^2=16>8，如果这个数越大越是如此，因此我们要计算一下，这个边界是多少。为此，解如下不等式：(a/2)^2 >= a

时间复杂度：O(logn)
空间复杂度：O(1)

```
class Solution:
    def mySqrt(self, x: int) -> int:
        left,right=1,x//2+1
        while left<=right:
            mid=left+(right-left)//2
            square=mid*mid
            if square==x:
                return mid
            elif square>x:
                right=mid-1
            else:
                left=mid+1
        return right
        
```

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

### 136.只出现一次的数字
    链接：https://leetcode-cn.com/problems/single-number/

    给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

    说明：

    你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

    示例 1:

    输入: [2,2,1]
    输出: 1
    示例 2:

    输入: [4,1,2,1,2]
    输出: 4

题解一|list：
```
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        temp=[]
        for i in nums:
            if i not in temp:
                temp.append(i)
            else:
                temp.remove(i)
        # return temp[0]
        return temp.pop()
```
题解二|hash：
```
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        temp={}
        for i in nums:
            if i not in temp:
                temp[i]=1
            else:
                temp[i]+=1
        for k,v in temp.items():
            if v==1:
                return k
```
题解三|位运算：

异或运算有以下三个性质。

    任何数和 0 做异或运算，结果仍然是原来的数，即 a⊕0=a。
    任何数和其自身做异或运算，结果是 0，即 a⊕a=0。
    异或运算满足交换律和结合律，即 a⊕b⊕a=b⊕a⊕a=b⊕(a⊕a)=b⊕0=b。

```
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        res=0
        for i in nums:
            res^=i
        return res
```

```
from functools import reduce # python2不需要引入

class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        return reduce(lambda x,y:x^y,nums)
```

### 137.只出现一次的数字 II
    链接：https://leetcode-cn.com/problems/single-number-ii/

    给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现了三次。找出那个只出现了一次的元素。

    说明：

    你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

    示例 1:

    输入: [2,2,3,2]
    输出: 3
    示例 2:

    输入: [0,1,0,1,0,1,99]
    输出: 99

题解一|hash：

```
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        hash={}
        for i in nums:
            if i not in hash:
                hash[i]=1
            else:
                hash[i]+=1
        for k,v in hash.items():
            if v!=3:
                return k
```

题解二|数学运算：

    3×(a+b+c)−(a+a+a+b+b+b+c)=2c

    时间复杂度：O(N)，遍历输入数组。
    空间复杂度：O(N)，存储 N/3N/3 个元素的集合。

```
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        return (3*sum(set(nums))-sum(nums))//2
```

？？？题解三|位运算:

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/or.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/or.png)



### 166. 分数到小数
    链接：https://leetcode-cn.com/problems/fraction-to-recurring-decimal/

    给定两个整数，分别表示分数的分子 numerator 和分母 denominator，以 字符串形式返回小数 。

    如果小数部分为循环小数，则将循环的部分括在括号内。

    如果存在多个答案，只需返回 任意一个 。

    对于所有给定的输入，保证 答案字符串的长度小于 104 。

    示例 1：

    输入：numerator = 1, denominator = 2
    输出："0.5"
    示例 2：

    输入：numerator = 2, denominator = 1
    输出："2"
    示例 3：

    输入：numerator = 2, denominator = 3
    输出："0.(6)"
    示例 4：

    输入：numerator = 4, denominator = 333
    输出："0.(012)"
    示例 5：

    输入：numerator = 1, denominator = 5
    输出："0.2"
     

    提示：

    -231 <= numerator, denominator <= 231 - 1
    denominator != 0

题解一|hash：

注意：

    正负号；
    是否有小数点，也就是能否被整除；
    如果有小数点的话，是否有循环；
    如果有循环，如何添加括号；

```
class Solution(object):
    def fractionToDecimal(self, numerator, denominator):
        """
        :type numerator: int
        :type denominator: int
        :rtype: str
        """
        if numerator == 0:
            return '0'
        res=[]
        if numerator ^ denominator < 0:
            res.append('-')
        
        numerator=abs(numerator)
        denominator=abs(denominator)

        a,b=divmod(numerator,denominator)
        res.append(str(a))
        if b==0:
            return ''.join(res)

        res.append('.')
        hash={}
        while b != 0:
            if b in hash:
                res.insert(hash[b],'(')
                res.append(')')
                break
            hash[b]=len(res)
            print(hash)
            b*=10
            a,b=divmod(b,denominator)
            res.append(str(a))
        return ''.join(res)
```

### 171.Excel表列序号
    链接：https://leetcode-cn.com/problems/excel-sheet-column-number/

    给定一个Excel表格中的列名称，返回其相应的列序号。

    例如，

        A -> 1
        B -> 2
        C -> 3
        ...
        Z -> 26
        AA -> 27
        AB -> 28 
        ...
    示例 1:

    输入: "A"
    输出: 1
    示例 2:

    输入: "AB"
    输出: 28
    示例 3:

    输入: "ZY"
    输出: 701

题解一：
```
class Solution:
    def titleToNumber(self, s: str) -> int: 
        res=0
        for each in s:
            num=(ord(each)-65)+1
            res=res*26+num
        return res
```

### 172. 阶乘后的零
    链接：https://leetcode-cn.com/problems/factorial-trailing-zeroes/

    给定一个整数 n，返回 n! 结果尾数中零的数量。

    示例 1:

    输入: 3
    输出: 0
    解释: 3! = 6, 尾数中没有零。
    示例 2:

    输入: 5
    输出: 1
    解释: 5! = 120, 尾数中有 1 个零.
    说明: 你算法的时间复杂度应为 O(log n) 。

题解一：

如果依靠算出阶乘结果，再计算有多少个0，很容易溢出。

思路：末尾有多少个0，只需要给当前数乘以一个10，就可以加一个0.

比如5!，也就是 5 * 4 * 3 * 2 * 1 = 120，我们发现结果会有一个 0，原因就是 2 和 5 相乘构成了一个 10。而对于 10 的话，其实也只有 2 * 5 可以构成，所以我们只需要找有多少对 2/5。

11! = 11 * 10 * 9 * 8 * 7 * 6 * 5 * 4 * 3 * 2 * 1 = 11 * (2 * 5) * 9 * (4 * 2) * 7 * (3 * 2) * (1 * 5) * (2 * 2) * 3 * (1 * 2) * 1

对于含有 2 的因子的话是 1 * 2, 2 * 2, 3 * 2, 4 * 2 ...

对于含有 5 的因子的话是 1 * 5, 2 * 5...

含有 2 的因子每两个出现一次，含有 5 的因子每 5 个出现一次，所有 2 出现的个数远远多于 5，换言之找到一个 5，一定能找到一个 2 与之配对。所以我们只需要找有多少个 5。

直接的，我们只需要判断每个累乘的数有多少个 5 的因子即可。

```
class Solution:
    def trailingZeroes(self, n: int) -> int:
        count=0
        for i in range(n+1):
            tmp=i
            while tmp>0:
                if tmp%5==0:
                    count+=1
                    tmp//=5
                else:
                    break
        return count
```

以上算法超时，继续优化。

对于一个数的阶乘，5 的因子一定是每隔 5 个数出现一次，也就是下边的样子。

    n! = 1 * 2 * 3 * 4 * (1 * 5) * ... * (2 * 5) * ... * (3 * 5) *... * n

因为每隔 5 个数出现一个 5，所以计算出现了多少个 5，我们只需要用 n/5 就可以算出来。

但还没有结束，继续分析。

    ... * (1 * 5) * ... * (1 * 5 * 5) * ... * (2 * 5 * 5) * ... * (3 * 5 * 5) * ... * n

每隔 25 个数字，出现的是两个 5，所以除了每隔 5 个数算作一个 5，每隔 25 个数，还需要多算一个 5。

也就是我们需要再加上 n / 25 个 5。

同理我们还会发现每隔 5 * 5 * 5 = 125 个数字，会出现 3 个 5，所以我们还需要再加上 n / 125 。

综上，规律就是每隔 5 个数，出现一个 5，每隔 25 个数，出现 2 个 5，每隔 125 个数，出现 3 个 5，以此类推。

最终 5 的个数就是 n / 5 + n / 25 + n / 125 ...

写程序的话，如果直接按照上边的式子计算，分母可能会造成溢出。所以算 n / 25 的时候，我们先把 n 更新，n = n / 5，然后再计算 n / 5 即可，后边的同理。

```
class Solution:
    def trailingZeroes(self, n: int) -> int:
        count=0
        while n>0:
            count+=n//5
            n//=5
        return count
```

### 202. 快乐数
    编写一个算法来判断一个数 n 是不是快乐数。

    「快乐数」定义为：对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和，然后重复这个过程直到这个数变为 1，也可能是 无限循环 但始终变不到 1。如果 可以变为  1，那么这个数就是快乐数。

    如果 n 是快乐数就返回 True ；不是，则返回 False 。

     

    示例：

    输入：19
    输出：true
    解释：
    12 + 92 = 82
    82 + 22 = 68
    62 + 82 = 100
    12 + 02 + 02 = 1

思路：

    根据我们的探索，我们猜测会有以下三种可能。

        最终会得到 11。
        最终会进入循环。
        值会越来越大，最后接近无穷大。

    根据我们的观察，发现不会出现无穷大的情况。

实现：

    给一个数字 n，它的下一个数字是什么？     做数位分离，求平方和。
    按照一系列的数字来判断我们是否进入了一个循环。     每次生成链中的下一个数字时，我们都会检查它是否已经在 HashSet 中。

时间复杂度：O(logn)
空间复杂度：O(logn)

题解一|set：
```
class Solution:
    def isHappy(self, n: int) -> bool:
        def getNext(n):
            ans=0
            while n>0:
                n,digits=divmod(n,10)
                ans+=pow(digits,2)
            return ans
        
        seen=set()
        while n!=1 and n not in seen:
            seen.add(n)
            n=getNext(n)
        return n==1
```

题解二|快慢指针：

时间复杂度：O(logn)
空间复杂度：O(1)
```
class Solution:
    def isHappy(self, n: int) -> bool:
        def getNext(n):
            ans=0
            while n>0:
                n,digits=divmod(n,10)
                ans+=pow(digits,2)
            return ans
        
        slow=n
        fast=getNext(n)
        while fast !=1 and fast != slow:
            slow=getNext(slow)
            fast=getNext(getNext(fast))
        return fast==1
```

题解三|数学：

如果这样做，您会发现只有一个循环：44→16→37→58→89→145→42→20→4。所有其他数字都在进入这个循环的链上，或者在进入 1 的链上。

时间复杂度：O(logn)
空间复杂度：O(1)
```
class Solution:
    def isHappy(self, n: int) -> bool:
        cycle={4, 16, 37, 58, 89, 145, 42, 20}
        
        def getNext(n):
            ans=0
            while n>0:
                n,digits=divmod(n,10)
                ans+=pow(digits,2)
            return ans
        
        while n!=1 and n not in cycle:
            n=getNext(n)
        return n==1

```
### 204. 计数质数
    统计所有小于非负整数 n 的质数的数量。

    示例 1：

    输入：n = 10
    输出：4
    解释：小于 10 的质数一共有 4 个, 它们是 2, 3, 5, 7 。
    示例 2：

    输入：n = 0
    输出：0
    示例 3：

    输入：n = 1
    输出：0
     

    提示：

    0 <= n <= 5 * 106

题解一|暴力|超时：

注意：题意是小于，而不是小于等于。

```
class Solution:
    def countPrimes(self, n: int) -> int:
        if n<2:
            return 0
        count=0
        for i in range(2,n):
            flag=True
            for j in range(2,i):
                if j!=i and i%j==0: # if i%j==0:
                    flag=False
                    break
            if flag:
                count+=1
        return count
```

 题解二|暴力优化：
 ```
 class Solution:
    def countPrimes(self, n: int) -> int:
        if n<3:
            return 0
        count=1
        for i in range(3,n):
            if i%2 == 0: # 排除偶数，使用if i^1==0:有可以
                continue
            flag=True
            for j in range(3,int(i**0.5)+1,2): # 因为排除偶数，加2会更快。
                if i%j==0:
                    flag=False
                    break
            if flag:
                count+=1
        return count
 ```

题解三|厄拉多塞筛法:

https://leetcode-cn.com/problems/count-primes/solution/ji-shu-zhi-shu-bao-li-fa-ji-you-hua-shai-fa-ji-you/

思路：
    
    选中数字2，并排除2的倍数；
    选中数字3，并排除3的倍数；
    选中数字5，并排除5的倍数；
    ...


```
class Solution:
    def countPrimes(self, n: int) -> int:
        count=0
        sign=[True]*n
        for i in range(2,n):
            if sign[i]:
                count+=1
                for j in range(i+i,n,i):
                    sign[j]=False
        return count
```

### 326. 3的幂
    链接：https://leetcode-cn.com/problems/power-of-three/

    给定一个整数，写一个函数来判断它是否是 3 的幂次方。

    示例 1:

    输入: 27
    输出: true
    示例 2:

    输入: 0
    输出: false
    示例 3:

    输入: 9
    输出: true
    示例 4:

    输入: 45
    输出: false
    进阶：
    你能不使用循环或者递归来完成本题吗？

题解一|循环：

时间复杂度：O(logb(n))，我们的例子中是 O(logn)，除数是用对数表示的。
空间复杂度：O(1)，没有使用额外的空间。

```
class Solution:
    def isPowerOfThree(self, n: int) -> bool:
        if n<1: # 不加这里，会在0里面陷入死循环
            return 0
        while n%3==0:
            n//=3
        return n==1
```

题解二|递归：
```
class Solution:
    def isPowerOfThree(self, n: int) -> bool:
        if n==1:
            return True
        if n<=0 or n%3!=0:
            return False
        n=n//3
        return self.isPowerOfThree(n)
```

题解三|换底公式：
```
class Solution:
    import math
    def isPowerOfThree(self, n: int) -> bool:
        if n<1:
            return False
        res=math.log10(n)/math.log10(3) # 不能使用math.log()，会有小数
        return True if res-int(res)==0 else False
        # return (math.log10(n) / math.log10(3))%1 == 0
```

### 412. Fizz Buzz
    链接：https://leetcode-cn.com/problems/fizz-buzz/

    写一个程序，输出从 1 到 n 数字的字符串表示。

    1. 如果 n 是3的倍数，输出“Fizz”；

    2. 如果 n 是5的倍数，输出“Buzz”；

    3.如果 n 同时是3和5的倍数，输出 “FizzBuzz”。

    示例：

    n = 15,

    返回:
    [
        "1",
        "2",
        "Fizz",
        "4",
        "Buzz",
        "Fizz",
        "7",
        "8",
        "Fizz",
        "Buzz",
        "11",
        "Fizz",
        "13",
        "14",
        "FizzBuzz"
    ]

题解一：
```
class Solution:
    def fizzBuzz(self, n: int) -> List[str]:
        res=[]
        for i in range(1,n+1):
            if i%3==0 and i%5==0:
                res.append('FizzBuzz')
            elif i%3==0:
                res.append('Fizz')
            elif i%5==0:
                res.append('Buzz')
            else:
                res.append(str(i))
        return res
```

题解二|递归：
```
class Solution:
    def __init__(self):
        self.res=[]

    def fizzBuzz(self, n: int) -> List[str]:
        if not n:
            self.res.reverse()
            return self.res
        if n%3==0 and n%5==0:
            self.res.append('FizzBuzz')
        elif n%3==0:
            self.res.append('Fizz')
        elif n%5==0:
            self.res.append('Buzz')
        else:
            self.res.append(str(n))
        return self.fizzBuzz(n-1)
```

题解三|hash：
```
class Solution:
    def fizzBuzz(self, n: int) -> List[str]:
        hash={3:'Fizz',5:'Buzz'}
        res=[]
        for i in range(1,n+1):
            tmp=''
            for key in hash.keys():
                if i%key==0:
                    tmp+=hash[key]
            if not tmp:
                tmp=str(i)
            res.append(tmp)
        return res
```
### 1502. 判断能否形成等差数列
    链接：https://leetcode-cn.com/problems/can-make-arithmetic-progression-from-sequence/

    给你一个数字数组 arr 。

    如果一个数列中，任意相邻两项的差总等于同一个常数，那么这个数列就称为 等差数列 。

    如果可以重新排列数组形成等差数列，请返回 true ；否则，返回 false 。

    示例 1：

    输入：arr = [3,5,1]
    输出：true
    解释：对数组重新排序得到 [1,3,5] 或者 [5,3,1] ，任意相邻两项的差分别为 2 或 -2 ，可以形成等差数列。
    示例 2：

    输入：arr = [1,2,4]
    输出：false
    解释：无法通过重新排序得到等差数列。
    
    提示：

    2 <= arr.length <= 1000
    -10^6 <= arr[i] <= 10^6

题解一|模拟：

时间复杂度：O(nlogn)。排序的时间代价为 O(nlogn)，遍历序列的时间代价是 O(n)，故渐进时间复杂度为 O(nlogn+n)=O(nlogn)。
空间复杂度：O(logn)。快速排序中使用的栈空间期望是 O(logn)。

```
class Solution:
    def canMakeArithmeticProgression(self, arr: List[int]) -> bool:
        arr.sort()
        for i in range(1,len(arr)-1):
            if arr[i]*2 != arr[i-1]+arr[i+1]:
                return False
        return True
```

### 1447. 最简分数
    链接：https://leetcode-cn.com/problems/simplified-fractions/

    给你一个整数 n ，请你返回所有 0 到 1 之间（不包括 0 和 1）满足分母小于等于  n 的 最简 分数 。分数可以以 任意 顺序返回。

    示例 1：

    输入：n = 2
    输出：["1/2"]
    解释："1/2" 是唯一一个分母小于等于 2 的最简分数。
    示例 2：

    输入：n = 3
    输出：["1/2","1/3","2/3"]
    示例 3：

    输入：n = 4
    输出：["1/2","1/3","1/4","2/3","3/4"]
    解释："2/4" 不是最简分数，因为它可以化简为 "1/2" 。
    示例 4：

    输入：n = 1
    输出：[]
     

    提示：

    1 <= n <= 100

题解一：
```
class Solution:
    def simplifiedFractions(self, n: int) -> List[str]:
        def gcd(x,y):
            while y:
                x,y=y,x%y
            return x
    
        res=[]
        tmp=set()
        for i in range(2,n+1):
            for j in range(1,i):
                c=gcd(i,j)
                a,b=j//c,i//c
                if (a,b) in tmp:
                    continue
                tmp.add((a,b))
                res.append(str(a)+'/'+str(b))
        return res
```