---
title: LeetCode-4-动态规划
date: 2020-01-19 11:28:41
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---

## 四、动态规划

动态规划三大步骤：

动态规划，无非就是利用历史记录，来避免我们的重复计算。而这些历史记录，我们得需要一些变量来保存，一般是用一维数组或者二维数组来保存。

第一步骤：定义数组元素的含义，上面说了，我们会用一个数组，来保存历史数组，假设用一维数组 dp[] 吧。这个时候有一个非常非常重要的点，就是规定你这个数组元素的含义，例如你的 dp[i] 是代表什么意思？

第二步骤：找出数组元素之间的关系式，我觉得动态规划，还是有一点类似于我们高中学习时的归纳法的，当我们要计算 dp[n] 时，是可以利用 dp[n-1]，dp[n-2]…..dp[1]，来推出 dp[n] 的，也就是可以利用历史数据来推出新的元素值，所以我们要找出数组元素之间的关系式，例如 dp[n] = dp[n-1] + dp[n-2]，这个就是他们的关系式了。而这一步，也是最难的一步，后面我会讲几种类型的题来说。

第三步骤：找出初始值。学过数学归纳法的都知道，虽然我们知道了数组元素之间的关系式，例如 dp[n] = dp[n-1] + dp[n-2]，我们可以通过 dp[n-1] 和 dp[n-2] 来计算 dp[n]，但是，我们得知道初始值啊，例如一直推下去的话，会由 dp[3] = dp[2] + dp[1]。而 dp[2] 和 dp[1] 是不能再分解的了，所以我们必须要能够直接获得 dp[2] 和 dp[1]的值，而这，就是所谓的初始值。

由了初始值，并且有了数组元素之间的关系式，那么我们就可以得到 dp[n] 的值了，而 dp[n] 的含义是由你来定义的，你想求什么，就定义它是什么，这样，这道题也就解出来了。

原文：https://mp.weixin.qq.com/s/pg-IJ8rA1duIzt5hW1Cycw

### ?5.最长回文子串
    链接：https://leetcode-cn.com/problems/longest-palindromic-substring/

    给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

    示例 1：

    输入: "babad"
    输出: "bab"
    注意: "aba" 也是一个有效答案。
    示例 2：

    输入: "cbbd"
    输出: "bb"

题解一|暴力破解：
    很明显，暴力法将选出所有子字符串可能的开始和结束位置，并检验它是不是回文。
    时间复杂度：O(n^2),往往利用python的切片可以很好的缩减复杂度
    如果不用切片，还需要遍历一次子字符串，时间复杂度就是O(n^3)
    空间复杂度：O(1)

```
class Solution:
    def longestPalindrome(self, s: str) -> str:
        if s==s[::-1]:
            return s

        maxLen=1
        ans=s[0]
        for i in range(0,len(s)-1):
            for j in range(i+1,len(s)):
                if j-i+1 > maxLen and self.isPalindrome(s[i:j+1]):
                    maxLen=j-i+1
                    ans=s[i:j+1]
        return ans

    def isPalindrome(self, s: str) -> bool:
        left,right=0,len(s)-1
        while left<right:
            while left<len(s) and not s[left].isalnum():
                left+=1
            while right >-1 and not s[right].isalnum():
                right-=1
            if left>right:
                return True
            if s[left].upper() != s[right].upper():
                return False
            else:
                left+=1
                right-=1
        return True
```

时间复杂度：O(N^3)，这里 N 是字符串的长度，枚举字符串的左边界、右边界，然后继续验证子串是否是回文子串，这三种操作都与 N 相关；
空间复杂度：O(1)，只使用到常数个临时变量，与字符串长度无关。

```
class Solution:
    # 暴力匹配（超时）
    def longestPalindrome(self, s: str) -> str:
        # 特判
        size = len(s)
        if size < 2:
            return s

        max_len = 1
        res = s[0]

        # 枚举所有长度大于等于 2 的子串
        for i in range(size - 1):
            for j in range(i + 1, size):
                if j - i + 1 > max_len and self.__valid(s, i, j):
                    max_len = j - i + 1
                    res = s[i:j + 1]
        return res

    def __valid(self, s, left, right):
        # 验证子串 s[left, right] 是否为回文串
        while left < right:
            if s[left] != s[right]:
                return False
            left += 1
            right -= 1
        return True
```

```
class Solution:
    def longestPalindrome(self, s: str) -> str:
        if s==s[::-1]:
            return s

        maxLen=1
        ans=s[0]
        for i in range(0,len(s)-1):
            for j in range(i+1,len(s)):
                if j-i+1 > maxLen and s[i:j+1]==s[i:j+1][::-1]: # arr='abb',arr[0:1]='a',右边是开区间
                    maxLen=j-i+1
                    ans=s[i:j+1]
        return ans
```

题解二|每个字母当成回文串的中心：

    考虑两种情况:回文串的长度为奇数或者偶数情况。

```
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n=len(s)
        self.res=''
        def helper(i,j):
            while i>= 0 and j<n and s[i]==s[j]:
                i-=1
                j+=1
            if len(self.res) < j-i-1:
                self.res=s[i+1:j]
                # print(i,self.res)
        
        for i in range(n):
            helper(i,i)
            helper(i,i+1) # 解决case为"cbbd",即解决回文串为偶数的情况
        return self.res
```

题解三|把每个字母当成回文串的结束：

```
class Solution:
    def longestPalindrome(self, s: str) -> str:
        if not s:
            return ""
        max_len = 1
        n = len(s)
        start = 0
        for i in range(1,n):
            even = s[i-max_len:i+1] # 偶数
            odd = s[i - max_len-1:i+1] # 奇数
            #print(even,odd)
            if i - max_len - 1 >= 0 and odd == odd[::-1]:
                start = i - max_len - 1
                max_len += 2
            elif i - max_len >=0 and even == even[::-1]:
                start = i - max_len
                max_len += 1
                
        #print(start,max_len)
        return s[start: start+max_len]
```

题解四|动态规划：
![](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/dp.png)

https://leetcode-cn.com/problems/longest-palindromic-substring/solution/zhong-xin-kuo-san-dong-tai-gui-hua-by-liweiwei1419/

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/dp2.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/dp2.png)

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/dp3.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/dp3.png)

    1、定义数组的定义
        dp[i][j] 表示子串 s[i,j] 是否为回文子串。
    2、找出数组的关系式
        dp[i][j] = (s[i] == s[j]) and dp[i + 1][j - 1]
    3、找到初始值

        试想如果 dp[l][r]=true，我们要判断 dp[l-1][r+1] 是否为回文。
        只需要判断字符串在(l-1)和（r+1)两个位置是否为相同的字符，是不是减少了很多重复计算。

        初始状态，l=r 时，此时 dp[l][r]=true
        状态转移方程，dp[l][r]=true 并且(l-1)和（r+1)两个位置为相同的字符，此时 dp[l-1][r+1]=true。

https://leetcode-cn.com/problems/longest-palindromic-substring/solution/gao-hao-dong-tai-gui-hua-he-zhong-xin-tuo-zhan-zhu/

```
class Solution:
    def longestPalindrome(self, s: str) -> str:
        if not s or len(s) <2:
            return s

        length=len(s)
        start=0
        end=0
        count=1

        dp=[[0]* length for i in range(length)]
        for r in range(1,length):
            for l in range(0,length):
                if (s[l]==s[r]) and (r-l<=2 or dp[l+1][r-1]):
                    dp[l][r]=True
                    if r-l+1> count:
                        count=r-l+1
                        start=l
                        end=r
        return s[start:end+1]
```

```
class Solution:
    def longestPalindrome(self, s: str) -> str:
        # 观察是否存在状态，例如 babab,可知aba是回文序列，那么可以看出babab也是回文序列，当b = b的时候
        # 因此我们可以用二维数组来i,j表示回文串的起始和结尾索引位置。
        # 状态转移方程dp[i,j] = dp[i+1][j-1] if s[i] == s[j] else False
        # 接下来看有没有特殊的边界情况存在，我们发现当0<=j-i<3的时候，只要满足s[i] == s[j]，无论里面是什么dp[i,j]都会是回文字串,将这种情况分开讨论，当j-i>=3的时候，进行状态转移
        # 初始值
        m = len(s)
        max_len = 1
        start_index = 0
        if m < 2:
            return s
        dp = [[False for _ in range(m)] for _ in range(m)]
        for j in range(1,m):
            for i in range(0,j):
                if s[i]  == s[j]:
                    if j-i < 3:
                        dp[i][j] = True
                    else:
                        dp[i][j] = dp[i+1][j-1]
                else:
                    dp[i][j] = False
                if dp[i][j]:
                    cur_len = j-i+1
                    if cur_len > max_len:
                        max_len = cur_len
                        start_index = i
        return s[start_index:start_index+max_len]
```

```
class Solution:
    def longestPalindrome(self, s: str) -> str:
        if(not s or len(s)==1):
            return s
        n=len(s)
        dp=[[False]*n for _ in range(n)]
        max_len=1
        start=0
        for i in range(n):
            dp[i][i]=True
            if(i<n-1 and s[i]==s[i+1]):
                dp[i][i+1]=True
                start=i
                max_len=2
        for l in range(3,n+1):
            for i in range(n+1-l):
                r=i+l-1
                if(s[i]==s[r] and dp[i+1][r-1]):
                    dp[i][r]=True
                    start=i
                    max_len=l
        return s[start:start+max_len]
        
```

### 10. 正则表达式匹配
    链接：https://leetcode-cn.com/problems/regular-expression-matching/

    给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。

    '.' 匹配任意单个字符
    '*' 匹配零个或多个前面的那一个元素
    所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。

    
    示例 1：

    输入：s = "aa" p = "a"
    输出：false
    解释："a" 无法匹配 "aa" 整个字符串。
    示例 2:

    输入：s = "aa" p = "a*"
    输出：true
    解释：因为 '*' 代表可以匹配零个或多个前面的那一个元素, 在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。
    示例 3：

    输入：s = "ab" p = ".*"
    输出：true
    解释：".*" 表示可匹配零个或多个（'*'）任意字符（'.'）。
    示例 4：

    输入：s = "aab" p = "c*a*b"
    输出：true
    解释：因为 '*' 表示零个或多个，这里 'c' 为 0 个, 'a' 被重复一次。因此可以匹配字符串 "aab"。
    示例 5：

    输入：s = "mississippi" p = "mis*is*p*."
    输出：false
    

    提示：

    0 <= s.length <= 20
    0 <= p.length <= 30
    s 可能为空，且只包含从 a-z 的小写字母。
    p 可能为空，且只包含从 a-z 的小写字母，以及字符 . 和 *。
    保证每次出现字符 * 时，前面都匹配到有效的字符

题解一|动态规划：

参考：https://leetcode-cn.com/problems/regular-expression-matching/solution/shou-hui-tu-jie-wo-tai-nan-liao-by-hyj8/

思路：

1、定义dp[i][j]：dp[i][j]代表s的前i个元素能否被p的前j个元素匹配

2、递推表达式

    (1) s[i-1] == p[j-1] or p[j-1]=='.'时：dp[i][j] = dp[i-1][j-1]=True；
    (2) s[i-1] != p[j-1]时：
            p[j-1]=='*'时：
                s[i-1]!=p[j-2]时：dp[i][j] = dp[i][j-2]
                s[i-1]==p[j-2] or p[j-2]=='*':
                    p[j-2]重复0次，dp[i][j]=dp[i][j-2]
                    p[j-2]重复1次，dp[i][j]=dp[i-1][j-2]
                    p[j-2]重复2次以上，dp[i][j]=dp[i-1][j]
3、初始值
    s和p为空，匹配。dp[i][j]=True
    s不为空，p为空，不匹配。dp[i][0]=False
    s为空，p不为空，如果要匹配，s='',p='a*',这样让p变成空，才能匹配。dp[0][j]=dp[0][j-2]=True

```
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        # if not s or not p: # 当s='',p='a*',此case无法通过。
        #     return False
        m,n=len(s),len(p)
        dp=[[False]*(n+1) for _ in range(m+1)] # dp[i][j]代表s的前i个元素能否被p的前j个元素匹配
        dp[0][0]=True
        for j in range(1,n+1): # 初始值
            if p[j-1]=='*':
                dp[0][j]=dp[0][j-2]
        for i in range(1,m+1):
            for j in range(1,n+1):
                if s[i-1]==p[j-1] or p[j-1]=='.':
                    dp[i][j]=dp[i-1][j-1]
                elif p[j-1]=='*':
                    if s[i-1]==p[j-2] or p[j-2]=='.':
                        dp[i][j]=dp[i][j-2] or dp[i-1][j-2] or dp[i-1][j]
                    else:
                        dp[i][j]=dp[i][j-2]
        return dp[m][n]

```

### 32. 最长有效括号
    链接：https://leetcode-cn.com/problems/longest-valid-parentheses/

    给定一个只包含 '(' 和 ')' 的字符串，找出最长的包含有效括号的子串的长度。

    示例 1:

    输入: "(()"
    输出: 2
    解释: 最长有效括号子串为 "()"
    示例 2:

    输入: ")()())"
    输出: 4
    解释: 最长有效括号子串为 "()()"

题解一|动态规划：

1、定义dp：dp[i]表示以i结尾的最长有效括号
2、递推表达式：
    
        s[i]=='(',所以dp[i]=0；
        s[i]==')':
            当s[i-1]=='(',所以dp[i]=dp[i-2]+2；
            当s[i-1]==')',且s[i-dp[i-1]-1]为'(',所以dp[i]= dp[i-1] + 2 + dp[i-dp[i-1]-2]；
3、初始化dp[i]=0

时间复杂度：O(n)
空间复杂度：O(n)

```
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        if not s:
            return 0
        dp=[0]*len(s)
        count=0
        for i in range(len(s)):
            if i>0 and s[i]==')':
                if s[i-1]=='(':
                    dp[i]=dp[i-2]+2
                # elif s[i-1]==')' and s[i - dp[i - 1] - 1] == "(": # 无法过case="(()))())("
                elif s[i-1]==')' and s[i - dp[i - 1] - 1] == "(" and i-dp[i-1]-1>=0:
                    dp[i]=dp[i-1]+2+dp[i-dp[i-1]-2]  # dp[i-dp[i-1]-2]解决case：()(())
            if dp[i]>count:
                count=dp[i]
        return count
```

```
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        count=0
        n=len(s)
        dp=[0]*n
        for i in range(1,n):
            if s[i]==')':
                if s[i-1]=='(':
                    if i-2 >= 0:
                        dp[i]=dp[i-2]+2
                    else:
                        dp[i]=2
                elif s[i-dp[i-1]-1]=='(' and i-dp[i-1]>0:
                    if i-dp[i-1]-2 >= 0:
                        dp[i]=dp[i-1]+2+dp[i-dp[i-1]-2]
                    else:
                        dp[i]=dp[i-1]+2
            count=max(count,dp[i])
        return count


```

题解二|栈：
```
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        if not s:
            return 0
        n=len(s)
        res=0
        stack=[-1]
        for i in range(n):
            if s[i]=='(':
                stack.append(i)
            else:
                stack.pop()
                if not stack: # 解决第一个字符为')'时，stack.pop()后为空越界的问题
                    stack.append(i)
                else:
                    res=max(res,i-stack[-1])
        return res
```

### 53.最大子序和
    链接：https://leetcode-cn.com/problems/maximum-subarray/

    给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

    示例:

    输入: [-2,1,-3,4,-1,2,1,-5,4],
    输出: 6
    解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
    进阶:

    如果你已经实现复杂度为 O(n) 的解法，尝试使用更为精妙的分治法求解。

参考：https://leetcode-cn.com/problems/maximum-subarray/solution/zui-da-zi-xu-he-cshi-xian-si-chong-jie-fa-bao-li-f/
题解一：

    遍历整个数组，在数组的每一个位置时，求出一个全局最大值L，代表以当前位置
    为结尾的最大字串，而G是当前位置的L和上一个位置L相比较后，取数值更大的。
    当遍历到i时，以i个为结尾的最大字串就是我们的L。

    l：代表以当前位置为结尾的最大字符
    g：当前位置l和上一个位置l取数值最大

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

```
class Solution:
    def maxSubArray(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        l=g=-10000000000
        for n in nums:
            l=max(n,l+n)
            g=max(l,g)
        return g
```
题解二：
```
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        for i in range(1,len(nums)):
            nums[i]=max(nums[i],nums[i]+nums[i-1])
            
        return max(nums) # 取子序和中最大值
```
题解三|动态规划:
    1、定义数组元素的含义

        dp[i]为以num[i]结尾的子段的最大子段和
        dp[1]为以num[1]结尾的最大子段和

    2、寻找递推表达式

        只考虑第一个元素，则最大子段和为其本身 dp[0] = nums[0]

        考虑前两个元素，最大子段和为 nums[0],num[1]以及 nums[0] + num[1] 中最大值 设为dp[1]

        考虑前三个元素，如何求其最大子段和？还是分为两种情况讨论，第三个元素在最后的字串内吗？

        若第三个元素也包含在最后的字串内，则dp[2] = Max(dp[1]+nums[2] , nums[2])

        dp[i]=max(dp[i-1]+num[i],num[i])

    3、找出初始值

        dp[0]=num[0]
        dp[1]=max(dp[0]+num[1],num[1])

```
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        lens=len(nums)
        if lens==0:
            return 0
        dp=lens*[0]
        dp[0]=nums[0]
        # dp[1]=max(dp[0]+nums[1],nums[1])
        res=dp[0]
        for i in range(1,lens):
            dp[i]=max(dp[i-1]+nums[i],nums[i])
            if dp[i]>res:
                res=dp[i]
        return res

输入：
[-2,1,-3,4,-1,2,1,-5,4]
输出：
[-2, 1, 0, 0, 0, 0, 0, 0, 0]
[-2, 1, -2, 0, 0, 0, 0, 0, 0]
[-2, 1, -2, 4, 0, 0, 0, 0, 0]
[-2, 1, -2, 4, 3, 0, 0, 0, 0]
[-2, 1, -2, 4, 3, 5, 0, 0, 0]
[-2, 1, -2, 4, 3, 5, 6, 0, 0]
[-2, 1, -2, 4, 3, 5, 6, 1, 0]
[-2, 1, -2, 4, 3, 5, 6, 1, 5]
```

### 62.不同路径
    链接：https://leetcode-cn.com/problems/unique-paths/

    一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

    机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

    问总共有多少条不同的路径？
![tree1](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/unique-path.png)

    例如，上图是一个7 x 3 的网格。有多少可能的路径？

    说明：m 和 n 的值均不超过 100。

    示例 1:

    输入: m = 3, n = 2
    输出: 3
    解释:
    从左上角开始，总共有 3 条路径可以到达右下角。
    1. 向右 -> 向右 -> 向下
    2. 向右 -> 向下 -> 向右
    3. 向下 -> 向右 -> 向右
    示例 2:

    输入: m = 7, n = 3
    输出: 28

题解一|动态规划：

1、定义数组元素的含义
    目的：从左上角到右下角一共有多少种路径
    dp[i][j]含义：从左上角走到(i,j)这个位置时，一共有dp[i][j]种路径

2、找到数组的递推关系式

    如何才能到达 (i, j) 这个位置呢？可以向下走或者向右走，所以有两种方式到达：

        一种是从 (i-1, j) 这个位置走一步到达
        一种是从(i, j - 1) 这个位置走一步到达

    所以：dp[i][j]=dp[i-1][j]+dp[i][j-1]
3、找到初始值
    
    当 dp[i][j] 中，如果 i 或者 j 有一个为 0，那么还能使用关系式吗？答是不能的，因为这个时候把 i - 1 或者 j - 1，就变成负数了，数组就会出问题了。

    图中的最上面一行和左边一列，因此初始值如下：

        dp[0][0….n-1] = 1; // 相当于最上面一行，只能一直往右走
        dp[0…m-1][0] = 1; // 相当于最左面一列，只能一直往下走

时间复杂度：O(m*n)
空间复杂度：O(m*n)，使用dp数组保存结果

```
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        if m<=0 or n<=0:
            return 0
        dp=[ n*[_] for _ in range(m)] # 定义二维数组
        # print(dp)
        for i in range(m):
            dp[i][0]=1
        for j in range(n):
            dp[0][j]=1
            
        for i in range(1,m):
            for j in range(1,n):
                dp[i][j]=dp[i-1][j]+dp[i][j-1]
        return dp[m-1][n-1]

```

```
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        if m<=0 or n<=0:
            return 0
        dp=[ m*[_] for _ in range(n)]
        # print(dp)
        for i in range(n):
            dp[i][0]=1
        for j in range(m):
            dp[0][j]=1
            
        for i in range(1,n):
            for j in range(1,m):
                dp[i][j]=dp[i-1][j]+dp[i][j-1]
        return dp[n-1][m-1]
```

题解二|动态规划优化:

参考：https://mp.weixin.qq.com/s?__biz=Mzg2NzA4MTkxNQ==&mid=2247486303&idx=1&sn=6034d1e6ca24253da64d6c539998f7c6&scene=21#wechat_redirect

转化成一维，关系式为 dp[i] = dp[i] + dp[i-1]

空间复杂度：O(n)
```
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        if m<=0 or n<=0:
            return 0
        dp=n*[0] # n是列
        for i in range(n):
            dp[i]=1
        
        for i in range(1,m):
            dp[0]=1 # 每行第一个值为1。
            for j in range(1,n):
                dp[j]=dp[j-1]+dp[j]
        return dp[n-1]
```
### 63.不同路径 II
    链接：https://leetcode-cn.com/problems/unique-paths-ii/

    一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

    机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

    现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

    如上图

    网格中的障碍物和空位置分别用 1 和 0 来表示。

    说明：m 和 n 的值均不超过 100。

    示例 1:

    输入:
    [
      [0,0,0],
      [0,1,0],
      [0,0,0]
    ]
    输出: 2
    解释:
    3x3 网格的正中间有一个障碍物。
    从左上角到右下角一共有 2 条不同的路径：
    1. 向右 -> 向右 -> 向下 -> 向下
    2. 向下 -> 向下 -> 向右 -> 向右


题解一|动态规划：

1、定义数组元素的含义
    机器人从左上角走到(i, j) 这个位置时，一共有 dp[i][j] 种路径
2、找出关系数组元素间的关系式
    想象以下，机器人要怎么样才能到达 (i, j)这个位置？由于机器人可以向下走或者向右走，所以有两种方式到达。当遇到障碍时

    一种是从 (i-1, j) 这个位置走一步到达

    一种是从(i, j-1) 这个位置走一步到达

    因为是计算所有可能的步骤，所以是把所有可能走的路径都加起来，所以关系式是 dp[i][j] = dp[i-1][j] + dp[i][j-1]。（当没有遇到障碍时，就用以上的方式进行）
3、找出初始值
    dp[0][0]=1

    dp[0][i]=dp[0][i-1]; // 相当于最上面一行，机器人只能一直往右走（当没有遇到障碍时）
    dp[i][0]=dp[i-1][0]; // 相当于最左面一列，机器人只能一直往下走（当没有遇到障碍时）

```
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        if obstacleGrid is None:
            return 0
        if obstacleGrid[0][0]==1 or obstacleGrid[-1][-1] == 1:
            return 0

        m=len(obstacleGrid)
        n=len(obstacleGrid[0])

        dp=[n*[0] for _ in range(m)]  # dp=[n*[_] for _ in range(m)]时结果有误

        dp[0][0]=1
        for i in range(1,m):
            if obstacleGrid[i][0] !=1:
                dp[i][0]=dp[i-1][0]

        for i in range(1,n):
            if obstacleGrid[0][i] !=1:
                dp[0][i]=dp[0][i-1]

        for i in range(1,m):
            for j in range(1,n):
                if obstacleGrid[i][j] != 1:
                    dp[i][j]=dp[i-1][j]+dp[i][j-1]
        # print(dp[m-1][n-1])
        return dp[-1][-1]

```

题解二|动态规划优化：

时间复杂度：O(mn)
空间复杂度：O(n)

```
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        m,n=len(obstacleGrid),len(obstacleGrid[0])
        if not obstacleGrid:
            return 0
        if obstacleGrid[0][0]==1 or obstacleGrid[m-1][n-1]==1:
            return 0
        dp=n*[0]
        dp[0]=1
        for i in range(m):
            for j in range(n):
                if obstacleGrid[i][j] == 1:
                    dp[j]=0
                    continue
                if j>=1:
                    dp[j]=dp[j-1]+dp[j]
        return dp[n-1]
            
```
### 64.最小路径和
    链接：https://leetcode-cn.com/problems/minimum-path-sum/

    给定一个包含非负整数的 m x n 网格，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

    说明：每次只能向下或者向右移动一步。

    示例:

    输入:
    [
      [1,3,1],
      [1,5,1],
      [4,2,1]
    ]
    输出: 7
    解释: 因为路径 1→3→1→1→1 的总和最小。

题解一|动态规划：
1、定义数组元素的含义
    目的是从左上角到右下角，最小路径和是多少。
    dp[i][j]含义：当从左上角走到(i,j)位置时，最小路径和为dp[i][j]

    定义二维数据，数组是从下标 0 开始算起的，所以右下角的位置是 (m-1, n - 1)，所以 dp[m-1][n-1] 就是我们所求。
2、找到数组的递推关系式

    如何才能到达 (i, j) 这个位置？由于可以向下走或者向右走，所以有两种方式到达：

        一种是从 (i-1, j) 这个位置走一步到达
        一种是从 (i, j-1) 这个位置走一步到达

    不过本次计算不是所有可能路径，而是计算哪一个路径和最小。

    所以：dp[i][j]=min(dp[i-1][j],dp[i][j-1])+arr[i][j]

3、找到初始值

    当 dp[i][j] 中，如果 i 或者 j 有一个为 0，那么还能使用关系式吗？答是不能的，因为这个时候把 i - 1 或者 j - 1，就变成负数了，数组就会出问题了，所以我们的初始值是计算出所有的 dp[0][0….n-1] 和所有的 dp[0….m-1][0]

    dp[0][j] = arr[0][j] + dp[0][j-1]; // 相当于最上面一行，只能一直往左走
    dp[i][0] = arr[i][0] + dp[i][0];  // 相当于最左面一列，只能一直往下走

时间复杂度：O(m*n)
空间复杂度：O(m*n)

```
class Solution:
    def minPathSum(self, grid: List[List[int]]) -> int:
        # print(grid)
        m=len(grid) # 行
        n=len(grid[0]) # 列
        if m<=0 or n<=0:
            return 0
        dp=[ n*[_] for _ in range(m)]
        # print(dp)
        dp[0][0]=grid[0][0] 
        
        for i in range(1,m):
            dp[i][0]=dp[i-1][0]+grid[i][0]

        for i in range(1,n):
            dp[0][i]=dp[0][i-1]+grid[0][i]
        
        for i in range(1,m):
            for j in range(1,n):
                dp[i][j]=min(dp[i-1][j],dp[i][j-1])+grid[i][j]
        # print(dp)

        return dp[m-1][n-1]

```
### 70.爬楼梯
    链接：https://leetcode-cn.com/problems/climbing-stairs/

    假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

    每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

    注意：给定 n 是一个正整数。

    示例 1：

    输入： 2
    输出： 2
    解释： 有两种方法可以爬到楼顶。
    1.  1 阶 + 1 阶
    2.  2 阶
    示例 2：

    输入： 3
    输出： 3
    解释： 有三种方法可以爬到楼顶。
    1.  1 阶 + 1 阶 + 1 阶
    2.  1 阶 + 2 阶
    3.  2 阶 + 1 阶

题解一|递归：
```
class Solution:
    def climbStairs(self, n: int) -> int:
        if n<=2:
            return n
        return self.climbStairs(n-1)+self.climbStairs(n-2)
```
题解二|动态规划：
1、定义数组元素的含义
    
    dp[i]含义：跳上第i个台阶总共有dp[i]种跳法；
    dp[n]即为我们所求结果。
2、找出数组元素间的关系式

    把一个规模比较大的问题分成几个规模比较小的问题，然后由小的问题推导出大的问题。
    到达第 n 级的台阶有两种方式：
        一种是从第 n-1 级跳上来
        一种是从第 n-2 级跳上来
    所以：dp[n] = dp[n-1] + dp[n-2]。
3、找出初始值
    
    n=1时，dp[1]=dp[0]+dp[-1]，不允许下标为-1，所以dp[1]=1
    n=0时，dp[0]=0
    n=2时，dp[2]=dp[1]+dp[0]=1，所以以上初始值定义不严谨，因为dp[2]=2

时间复杂度：O(n)
空间复杂度：O(n)

```
class Solution:
    def climbStairs(self, n: int) -> int:
        if n<=2:
            return n
        dp=(n+1)*[0] # 注意是n+1
        dp[1]=1
        dp[2]=2
        for i in range(3,n+1):
            dp[i]=dp[i-1]+dp[i-2]
        return dp[n] # 返回最后一个数
```
### 72.编辑距离
    链接：https://leetcode-cn.com/problems/edit-distance/

    给定两个单词 word1 和 word2，计算出将 word1 转换成 word2 所使用的最少操作数 。

    你可以对一个单词进行如下三种操作：

    插入一个字符
    删除一个字符
    替换一个字符
    示例 1:

    输入: word1 = "horse", word2 = "ros"
    输出: 3
    解释: 
    horse -> rorse (将 'h' 替换为 'r')
    rorse -> rose (删除 'r')
    rose -> ros (删除 'e')
    示例 2:

    输入: word1 = "intention", word2 = "execution"
    输出: 5
    解释: 
    intention -> inention (删除 't')
    inention -> enention (将 'i' 替换为 'e')
    enention -> exention (将 'n' 替换为 'x')
    exention -> exection (将 'n' 替换为 'c')
    exection -> execution (插入 'u')

思路：

```
if s1[i] == s2[j]:
    啥都别做（skip）
    i, j 同时向前移动
else:
    三选一：
        插入（insert）
        删除（delete）
        替换（replace）
```

题解一|递归|自顶向下：
```
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        def dp(i,j):
            if i==-1:
                return j+1
            if j==-1:
                return i+1
            if word1[i]==word2[j]:
                return dp(i-1,j-1) # 什么都不做
            else:
                return min(
                    dp(i,j-1)+1, # 插入
                    dp(i-1,j)+1, # 删除
                    dp(i-1,j-1)+1 # 替换
                    )
        return dp(len(word1)-1,len(word2)-1)
```

解释：
```
if s1[i] == s2[j]:
    return dp(i - 1, j - 1)  # 啥都不做
# 解释：
# 本来就相等，不需要任何操作
# s1[0..i] 和 s2[0..j] 的最小编辑距离等于
# s1[0..i-1] 和 s2[0..j-1] 的最小编辑距离
# 也就是说 dp(i, j) 等于 dp(i-1, j-1)

dp(i, j - 1) + 1,    # 插入
# 解释：
# 我直接在 s1[i] 插入一个和 s2[j] 一样的字符
# 那么 s2[j] 就被匹配了，前移 j，继续跟 i 对比
# 别忘了操作数加一

dp(i - 1, j) + 1,    # 删除
# 解释：
# 我直接把 s[i] 这个字符删掉
# 前移 i，继续跟 j 对比
# 操作数加一

dp(i - 1, j - 1) + 1 # 替换
# 解释：
# 我直接把 s1[i] 替换成 s2[j]，这样它俩就匹配了
# 同时前移 i，j 继续对比
# 操作数加一
```

优化：
```
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        memo=dict()

        def dp(i,j):
            if (i,j) in memo:
                return memo[(i,j)]
            if i==-1:
                return j+1
            if j==-1:
                return i+1
            if word1[i]==word2[j]:
                memo[(i,j)] = dp(i-1,j-1) # 什么都不做
            else:
                memo[(i,j)]= min(
                    dp(i,j-1)+1, # 插入
                    dp(i-1,j)+1, # 删除
                    dp(i-1,j-1)+1 # 替换
                    )
            return memo[(i,j)]
        return dp(len(word1)-1,len(word2)-1)
```


题解二|动态规划：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/dp4.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/dp4.png)

思路：

    理解 word1 上的删除等价 word2 上的增加, word1 上的增加等价于 word2 上的删除
    2维的 dp array 中某一点 dp[i][j] 的意义: word1[ : i] 到 word[ : j] 转换所需要的最小的数目
    不太需要关注具体进行了删除、增加还是替换操作，而是专注于 a 状态到 b 状态所需要的步数

```
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        m = len(word1)
        n = len(word2)
        dp = [[float('inf') for _ in range(n + 1)] for _ in range(m + 1)]
        # 初始化
        for i in range(m + 1):
            dp[i][0] = i
        for i in range(n + 1):
            dp[0][i] = i
        # 状态转移
        # i , j 代表 word1, word2 对应位置的 index
        for i in range(1, m + 1):
            for j in range(1, n + 1):
                # 如果word1[:i][-1]==word2[:j][-1]
                if word1[i - 1] == word2[j - 1]:
                    dp[i][j] = dp[i - 1][j - 1]
                # 否则从三种状态中选一个最小的然后 +1
                else:
                    dp[i][j] = min(dp[i - 1][j - 1], min(dp[i - 1][j], dp[i][j - 1])) + 1
        return dp[m][n]
```

题解三|动态规划：

    参考：https://mp.weixin.qq.com/s?__biz=Mzg2NzA4MTkxNQ==&mid=2247486294&idx=1&sn=dd8968700d19ea8b1db29065dc2f7b01&scene=21#wechat_redirect

1、定义数组元素的含义

    目的：求将 word1 转换成 word2 所使用的最少操作数 。

    dp[i][j]的含义为：当字符串 word1 的长度为 i，字符串 word2 的长度为 j 时，将 word1 转化为 word2 所使用的最少操作次数为 dp[i][j]。
2、找出关系数组元素间的关系式

    第一种：如果我们 word1[i] 与 word2 [j] 相等，这个时候不需要进行任何操作，所以 dp[i][j] = dp[i-1][j-1]。

    第二种：如果我们 word1[i] 与 word2 [j] 不相等，这个时候我们就必须进行调整，而调整的操作有 3 种，我们要选择一种。
    三种操作对应的关系式如下（注意字符串与字符的区别）：

    （1）、如果把字符 word1[i] 替换成与 word2[j] 相等，则有 dp[i][j] = dp[i-1][j-1] + 1;

    （2）、如果在字符串 word1末尾插入一个与 word2[j] 相等的字符，则有 dp[i][j] = dp[i][j-1] + 1;

    （3）、如果把字符 word1[i] 删除，则有 dp[i][j] = dp[i-1][j] + 1;

    所以应该选择一种操作，使得 dp[i][j] 的值最小，显然关系式有：dp[i][j] = min(dp[i-1] [j-1]，dp[i][j-1]，dp[i-1][j]) + 1;
3、找出初始值

    当 dp[i][j] 中，如果 i 或者 j 有一个为 0，那么还能使用关系式吗？答是不能的，因为这个时候把 i - 1 或者 j - 1，就变成负数了，数组就会出问题了，所以我们的初始值是计算出所有的 dp[0][0….n] 和所有的 dp[0….m][0]。

    这个还是非常容易计算的，因为当有一个字符串的长度为 0 时，转化为另外一个字符串，那就只能一直进行插入或者删除操作了。

时间复杂度：O(m*n)
空间复杂度：O(m*n)

```
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        m=len(word1)+1
        n=len(word2)+1
        dp = [ n*[_] for _ in range(m) ]
        for i in range(1,m):
            dp[i][0]=dp[i-1][0]+1
        
        for i in range(1,n):
            dp[0][i]=dp[0][i-1]+1
        
        for i in range(1,m):
            for j in range(1,n):
                if word1[i-1]==word2[j-1]:
                    dp[i][j]=dp[i-1][j-1]
                else:
                    dp[i][j]=min(dp[i-1][j-1],dp[i-1][j],dp[i][j-1])+1
        # print(dp)
        # print(dp[-1][-1])
        return dp[m-1][n-1]
```

???题解四|动态规划优化：

定义额外变量 pre = (i-1,j-1) 的值
二维的dp[i][j]=min(dp[i-1][j],dp[i-1][j-1],dp[i][j-1])+1
转换为一维的dp[i]=min(dp[i-1],pre,dp[i])+1

空间复杂度：O(n)
```
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        m=len(word1)+1
        n=len(word2)+1
        dp = n*[0]

        for i in range(0,n):
            dp[i]=i
        
        for i in range(1,m):
            tmp=dp[0]
            dp[0]=i
            for j in range(1,n):
                pre=tmp
                tmp=dp[j]
                if word1[i-1]==word2[j-1]:
                    dp[j]=pre
                else:
                    dp[j]=min(dp[j-1],pre,dp[j])+1
        return dp[n-1]
```

### 91.解码方法
    链接：https://leetcode-cn.com/problems/decode-ways/

    一条包含字母 A-Z 的消息通过以下方式进行了编码：

    'A' -> 1
    'B' -> 2
    ...
    'Z' -> 26
    给定一个只包含数字的非空字符串，请计算解码方法的总数。

    示例 1:

    输入: "12"
    输出: 2
    解释: 它可以解码为 "AB"（1 2）或者 "L"（12）。
    示例 2:

    输入: "226"
    输出: 3
    解释: 它可以解码为 "BZ" (2 26), "VF" (22 6), 或者 "BBF" (2 2 6) 。

题解一|动态规划：

    此题类似于青蛙跳台阶问题，不同的只是多了很多限制条件。

    青蛙跳台阶时，可以选择跳一级，也可以选择跳两级，所以青蛙到达第 n 级的台阶有两种方式：
    一种是从第 n-1 级跳上来；
    一种是从第 n-2 级跳上来；

    解码也同理，最大编码为26，所以第 n 个位置编码的总数也有两种方式：
    一种是从第 n-1 编码位置的总数；
    一种是从第 n-2 编码位置的总数；

    1、定义数组元素的含义
    第 i 位置的编码总数有 dp[i] 个
    d[i]代表第i个位置解码方法的总数

    2、找出关系数组元素间的关系式
    d[i]=d[i-1]+d[i-2]
    d[i+1]=d[i]+d[i-1]

    3、找出初始值
    dp[0]=1
    dp[1]=1
    dp[2]=2(此题限制2不好，因为‘10’返回1，’27‘返回0)

    4、限制条件
    字符串以‘0’开头时返回0
    字符串含有‘00’时返回0
    字符串中s[i]='0'但是int(s[i-1])>2时返回0
    字符串s[i-1:i]>26时，此时应该分开计算。dp[i]+=dp[i-1]

```
class Solution:
    def numDecodings(self, s: str) -> int:
    
        if s[0]=='0' or s is None:
            return 0
        if len(s)==1:
            return 1
        if len(s)==2:
            return 2

        dp=[0]*(len(s))
        dp[0]=1
        dp[1]=1
        dp[2]=2
        for i in range(2,len(s)):
            if s[i] != '0':
                print(i)
                dp[i]+=dp[i-1]
            if s[i-1] =='1' and (s[i-1]=='2' or s[i]<=6):
                dp[i]+=dp[i-2]
            if s[i]=='0' and s[i-1]=='0':
                return 0
        return dp[-1]
                
用例‘10’这个例子预期输出应该是1,但是实际输出为2，所以for循环从2开始有问题。
即使改成如下也有问题：
        if s=='10' or s=='20':
            return 1
        elif int(s)>26:
            return 0
        elif len(s)==2:
            return 2
```

```
class Solution:
    def numDecodings(self, s: str) -> int:
    
        if s[0]=='0' or s is None:
            return 0
        if len(s)==1:
            return 1
    
        length=len(s)
        dp=[0]*(length+1)
        dp[0]=1
        dp[1]=1

        for i in range(1,len(s)):
            if s[i] != '0':
                dp[i+1]+=dp[i]
            if s[i-1] =='1' or (s[i-1]=='2' and int(s[i])<=6):
                dp[i+1]+=dp[i-1]
            if s[i]=='0' and s[i-1]=='0':
                return 0
        # print(dp[-1])
        return dp[length]
```
### 95. 不同的二叉搜索树 II
    链接：https://leetcode-cn.com/problems/unique-binary-search-trees-ii/

    给定一个整数 n，生成所有由 1 ... n 为节点所组成的二叉搜索树。

    示例:

    输入: 3
    输出:
    [
      [1,null,3,2],
      [3,2,null,1],
      [3,1,null,null,2],
      [2,1,3],
      [1,null,2,null,3]
    ]
    解释:
    以上的输出对应以下 5 种不同结构的二叉搜索树：

       1         3     3      2      1
        \       /     /      / \      \
         3     2     1      1   3      2
        /     /       \                 \
       2     1         2                 3

题解一|递归：
![tree3](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/tree3.png)
![tree4](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/tree4.png)
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def generateTrees(self, n: int) -> List[TreeNode]:
        if n:
            return self.build(1,n)
        else:
            []
    def build(self,start,end):
        # 结束条件
        if start>end:
            return [None,]
        
        trees=[]
        for i in range(start,end+1):
            leftTrees=self.build(start,i-1)
            rightTrees=self.build(i+1,end)
            for l in leftTrees:
                for r in rightTrees:
                    currentTree=TreeNode(i)
                    currentTree.left=l
                    currentTree.right=r
                    trees.append(currentTree)
        # print(trees)
        return trees
```
### 96.不同的二叉搜索树
    链接：https://leetcode-cn.com/problems/unique-binary-search-trees/

    给定一个整数 n，求以 1 ... n 为节点组成的二叉搜索树有多少种？

    示例:

    输入: 3
    输出: 5
    解释:
    给定 n = 3, 一共有 5 种不同结构的二叉搜索树:

       1         3     3      2      1
        \       /     /      / \      \
         3     2     1      1   3      2
        /     /       \                 \
       2     1         2                 3

题解一|动态规划:

二叉树定义：

二叉查找树（Binary Search Tree），（又：二叉搜索树，二叉排序树）它或者是一棵空树，或者是具有下列性质的二叉树： 若它的左子树不空，则左子树上所有结点的值均小于它的根结点的值； 若它的右子树不空，则右子树上所有结点的值均大于它的根结点的值； 它的左、右子树也分别为二叉排序树。

参考：https://leetcode-cn.com/problems/unique-binary-search-trees/solution/bu-tong-de-er-cha-sou-suo-shu-by-leetcode/

方法：

    给定一个有序序列 1 ... n，为了根据序列构建一棵二叉搜索树。我们可以遍历每个数字 i，将该数字作为树根，1 ... (i-1) 序列将成为左子树，(i+1) ... n 序列将成为右子树。于是，我们可以递归地从子序列构建子树。
    在上述方法中，由于根各自不同，每棵二叉树都保证是独特的。

    可见，问题可以分解成规模较小的子问题。因此，我们可以存储并复用子问题的解，而不是递归的（也重复的）解决这些子问题，这就是动态规划法。

函数定义：
    
    令G(n)的从1到n可以形成二叉搜索树个数
    令f(i)为以i为根的二叉搜索树的个数

    所以G(n)是解决问题的函数：G(n) = f(1) + f(2) + f(3) + f(4) + ... + f(n)

![tree](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/tree.png)

1、定义数组元素的含义

    G[i]代表二叉搜索树的个数

2、找出数组之间的的关系式
    以i为根，[0,i-1]为左节点，[i+1,n]为右节点
    之后再对[0,i-1]和[i+1,n]递归求解

    f(i)=G(i-1)+G(n-i)
    G(n)=f(1) + f(2) + f(3) + f(4) + ... + f(n)
        =G(0)G(n-1)+G(1)G(n-2)+...+G(n-1)G(0)
    
3、找出初始值
    G[0]=1
    G[1]=1
    G[2]=2

```
class Solution:
    def numTrees(self, n: int) -> int:
        dp=[0]*(n+1)
        dp[0]=1
        dp[1]=1

        for i in range(2,n+1):
            for j in range(1,i+1):
                dp[i]+=dp[j-1]*dp[i-j]
        # print(dp)
        return dp[n]
```

### 118.杨辉三角
    链接：https://leetcode-cn.com/problems/pascals-triangle/

    给定一个非负整数 numRows，生成杨辉三角的前 numRows 行。

    在杨辉三角中，每个数是它左上方和右上方的数的和。

    示例:

    输入: 5
    输出:
    [
         [1],
        [1,1],
       [1,2,1],
      [1,3,3,1],
     [1,4,6,4,1]
    ]

题解一：
```
class Solution:
    def generate(self, numRows: int) -> List[List[int]]:
        triangle=[]
        for i in range(numRows):
            row=[None for _ in range(i+1)]
            row[0],row[-1]=1,1
            for j in range(1,len(row)-1):
                row[j]=triangle[i-1][j-1]+triangle[i-1][j]
            triangle.append(row)
        return triangle
```

题解二|动态规划：

    1、定义数组元素的含义
        dp存储每个数

    2、找出数组之间的的关系式
        遍历dp，将为0的位置使用动态规划填入，公式：dp[i][j]=dp[i-1][j-1]+dp[i-1][j]
        
    3、找出初始值
        初始化结果数组，numRows表示结果数组dp的行数，每一行的元素个数等于所处第几行。全部初始化为0；
        将边界全部初始化为1；

时间复杂度：O(n^2)，等差数列求和。
空间复杂度：O(n^2)，等差数列求和。

```
class Solution:
    def generate(self, numRows: int) -> List[List[int]]:
        dp=[[0]*i for i in range(1,numRows+1)]
        for i in range(numRows):
            dp[i][0]=dp[i][-1]=1
        for i in range(0,numRows):
            for j in range(i+1):
                if dp[i][j]==0:
                    dp[i][j]=dp[i-1][j-1]+dp[i-1][j]
        return dp
```

```
class Solution:
    def generate(self, numRows: int) -> List[List[int]]:
        dp=[[1]*i for i in range(1,numRows+1)]
        for i in range(2,numRows):
            for j in range(1,i):
                dp[i][j]=dp[i-1][j-1]+dp[i-1][j]
        return dp
```
### 119. 杨辉三角 II
    链接：https://leetcode-cn.com/problems/pascals-triangle-ii/
    
    给定一个非负索引 k，其中 k ≤ 33，返回杨辉三角的第 k 行。

    在杨辉三角中，每个数是它左上方和右上方的数的和。

    示例:

    输入: 3
    输出: [1,3,3,1]
    进阶：你可以优化你的算法到 O(k) 空间复杂度吗？

题解一|动态规划：
```
class Solution:
    def getRow(self, rowIndex: int) -> List[int]:
        dp=[[0]* i for i in range(1,rowIndex+2)] # 从1开始的，所以rowIndex+2
        for i in range(rowIndex+1):
            dp[i][0]=dp[i][-1]=1
        for i in range(rowIndex+1):
            for j in range(i+1):
                if dp[i][j]==0:
                    dp[i][j]=dp[i-1][j-1]+dp[i-1][j]
        return dp[rowIndex]
```

题解二|动态规划优化：
```
class Solution:
    def getRow(self, rowIndex: int) -> List[int]:
        dp=[1 for i in range(rowIndex+1)]
        for i in range(1,rowIndex+1):
            for j in range(i-1,0,-1):
                print(j,)
                dp[j]=dp[j-1]+dp[j]
        return dp
```

```
class Solution:
    def getRow(self, rowIndex: int) -> List[int]:
        dp = [1] * (rowIndex+1)
        for i in range(2, rowIndex+1):
            for j in range(1, i):
                index = i-j
                dp[index] = dp[index] + dp[index-1]
        return dp

```

### 120.三角形最小路径和
    链接：https://leetcode-cn.com/problems/triangle/

    给定一个三角形，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。

    例如，给定三角形：

    [
         [2],
        [3,4],
       [6,5,7],
      [4,1,8,3]
    ]
    自顶向下的最小路径和为 11（即，2 + 3 + 5 + 1 = 11）。

    说明：

    如果你可以只使用 O(n) 的额外空间（n 为三角形的总行数）来解决这个问题，那么你的算法会很加分。

题解一|动态规划：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/dp1.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/dp1.png)

1、定义数组的含义

    dp[i][j]为最小路径和
2、找出递推关系式
    
    从第二行开始，遍历区间[1,n):
        对每一层的元素进行遍历，遍历区间[0,len(triangle[i])，存在3种情况：
            首位；
            末位；
            其他元素；
3、找到初始值

```
class Solution:
    def minimumTotal(self, triangle: List[List[int]]) -> int:
        if triangle is None:
            return 0
        m=len(triangle)
        # print(triangle,m,triangle[0]) # m=4,triangle[0]=[2]
        if m==1:
            return triangle[0][0]
        dp=triangle
        for i in range(1,m):
            for j in range(len(triangle[i])):
                if j==0:
                    dp[i][j]=triangle[i-1][j]+triangle[i][j]
                elif j== len(triangle[i])-1:
                    dp[i][j]=triangle[i-1][j-1]+triangle[i][j]
                else:
                    dp[i][j]=min(triangle[i-1][j],triangle[i-1][j-1])+triangle[i][j]
        return min(dp[-1])
```

优化一下，空间复杂度变为O(n).
```
class Solution:
    def minimumTotal(self, triangle: List[List[int]]) -> int:
        if triangle is None:
            return 0
        m=len(triangle)
        if m==1:
            return triangle[0][0]
        for i in range(1,m):
            for j in range(len(triangle[i])):
                if j==0:
                    triangle[i][j]+=triangle[i-1][j]
                elif j== len(triangle[i])-1:
                    triangle[i][j]+=triangle[i-1][j-1]
                else:
                    triangle[i][j]+=min(triangle[i-1][j],triangle[i-1][j-1])
        return min(triangle[-1]) # 返回最后一行的最小值

```

### 139.单词拆分
    链接：https://leetcode-cn.com/problems/word-break/

    给定一个非空字符串 s 和一个包含非空单词列表的字典 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。

    说明：

    拆分时可以重复使用字典中的单词。
    你可以假设字典中没有重复的单词。
    示例 1：

    输入: s = "leetcode", wordDict = ["leet", "code"]
    输出: true
    解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。
    示例 2：

    输入: s = "applepenapple", wordDict = ["apple", "pen"]
    输出: true
    解释: 返回 true 因为 "applepenapple" 可以被拆分成 "apple pen apple"。
         注意你可以重复使用字典中的单词。
    示例 3：

    输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
    输出: false

题解一|动态规划：

1、定义数组的含义

    dp=[False,...,false],长度为n+1，n为字符串长度。
    dp[i]表示s的前i位是否可以用wordDict中的单词表示。

2、找出递推关系式
    
    遍历字符串的所有子串，遍历开始索引i，遍历区间[0,n)：
        遍历结束索引j，遍历区间[i+1,n+1)：
            若dp[i]=True且s[i,⋯,j)在wordlist中：dp[j]=True

            解释：dp[i]=True说明s的前i位可以用wordDict表示，则s[i,⋯,j)出现在wordDict中，说明s的前j位可以表示。

    返回dp[n]
3、找到初始值
    
    初始化dp[0]=True，空字符可以被表示。

时间复杂度：O(n^2)
空间复杂度：O(n)

```
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        n=len(s)
        dp=(n+1)*[False]
        dp[0]=True
        for i in range(n):
            for j in range(i+1,n+1):
                if dp[i] and s[i:j] in wordDict:
                    dp[j]=True
        # return dp[n]
        return dp[-1]
```

题解二|DFS|记忆化递归：
```
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        memo=[None] * len(s)
        def dfs(first):
            if first >= len(s):
                return True
            if memo[first] != None:
                return memo[first]

            for i in range(first,len(s)):
                if s[first:i+1] in wordDict and dfs(i+1):
                    memo[first]=True
                    return True
            memo[first]=False
            return False

        return dfs(0)
```

??题解三|记忆化回溯）:

    1、使用记忆化函数，保存出现过的backtrack(s)，避免重复计算。

    2、定义回溯函数backtrack(s)
    
        若s长度为0，则返回True，表示已经使用wordDict中的单词分割完。
        初试化当前字符串是否可以被分割res=False
        遍历结束索引i，遍历区间[1,n+1)：
            若s[0,⋯,i−1]在wordDict中：res=backtrack(s[i,...,n-1]) or  res。
            解释：保存遍历结束索引中，可以使字符串切割完成的情况。
        返回res

    3、返回backtrack(s)

```
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        import functools
        @functools.lru_cache(None)
        def back_track(s):
            if (not s):
                return True
            res=False
            for i in range(1,len(s)+1):
                if(s[:i] in wordDict):
                    res=back_track(s[i:]) or res
            return res
        return back_track(s)
```

题解四|BFS|超时：
```
import collections

class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        visited=[False]*len(s)
        queue=collections.deque()
        queue.append(0)

        while queue:
            i=queue.popleft()
            if visited[i]:
                continue
            else:
                visited[i]=False

            for j in range(i,len(s)):
                if s[i:j+1] in wordDict:
                    if j == len(s)-1:
                        return True
                    else:
                        queue.append(j+1)
        return False
```

### 152. 乘积最大子数组
    链接：https://leetcode-cn.com/problems/maximum-product-subarray/

    给定一个整数数组 nums ，找出一个序列中乘积最大的连续子序列（该序列至少包含一个数）。

    示例 1:

    输入: [2,3,-2,4]
    输出: 6
    解释: 子数组 [2,3] 有最大乘积 6。
    示例 2:

    输入: [-2,0,-1]
    输出: 0
    解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。

题解一|动态规划:

此题类似于52题最大子序和，唯一的区别是：由于存在负数，那么会导致最大的变最小的，最小的变最大的，因此还需要维护当前最小值minTmp，minTmp = min(minTmp * nums[i], nums[i])

1、定义数组元素的含义

    dp[i]为以num[i]结尾的子段的最大子段乘积
    dp[1]为以num[1]结尾的最大子段乘积
2、寻找递推表达式

    只考虑第一个元素，则最大子段和为其本身 dp[0] = nums[0]

    考虑前两个元素，最大子段和为 nums[0],num[1]以及 nums[0]*num[1] 中最大值 设为dp[1]

    考虑前三个元素，如何求其最大子段乘积？还是分为两种情况讨论，第三个元素在最后的字串内吗？

    若第三个元素也包含在最后的字串内，则dp[2] = Max(dp[1]*nums[2] , nums[2])

    dp[i]=max(dp[i-1]*num[i],num[i])
    由于存在负数：
    dp_[i]=min(dp_[i-1]*num[i],num[i])
3、找出初始值

    dp[0]=num[0]
    dp[1]=max(dp[0]* num[1],num[1])

```
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        lens=len(nums)
        if lens==0:
            return 0
        dp=lens*[0]
        mindp=lens*[0]
        dp[0]=nums[0]
        mindp[0]=nums[0]
        res=nums[0]
        for i in range(1,lens):
            dp[i]=max(dp[i-1]*nums[i],nums[i],mindp[i-1]*nums[i])
            mindp[i]=min(dp[i-1]*nums[i],nums[i],mindp[i-1]*nums[i])
            res=max(res,dp[i])
        return res
```

```
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        lens=len(nums)
        if lens==0:
            return 0
        res=float('-inf')
        maxTmp=1
        minTmp=1
        for i in range(lens):
            if nums[i]<0:
                maxTmp,minTmp=minTmp,maxTmp
            maxTmp=max(maxTmp*nums[i],nums[i])
            minTmp=min(minTmp*nums[i],nums[i])

            res=max(res,maxTmp)
        return res
```

### 221.最大正方形
    链接：https://leetcode-cn.com/problems/maximal-square/

    在一个由 0 和 1 组成的二维矩阵内，找到只包含 1 的最大正方形，并返回其面积。

    示例:

    输入: 

    1 0 1 0 0
    1 0 1 1 1
    1 1 1 1 1
    1 0 0 1 0

    输出: 4

题解一|动态规划：

1、定义数组元素的含义

    dp[i][j]表示以(i,j)为右下角，且只包含1的正方形边长最大值;
    计算出所有 dp(i,j) 的值，那么其中的最大值即为矩阵中只包含 1 的正方形的边长最大值，其平方即为最大正方形的面积。

2、寻找递推表达式

    如果该位置的值是 0，则 dp(i,j)=0，因为当前位置不可能在由 1 组成的正方形中；

    如果该位置的值是 1，则 dp(i,j) 的值由其上方、左方和左上方的三个相邻位置的 dp值决定。具体而言，当前位置的元素值等于三个相邻位置的元素中的最小值加 1，状态转移方程如下：

    dp(i,j)=min(dp(i−1,j),dp(i−1,j−1),dp(i,j−1))+1

3、找出初始值

    if matrix[i][j]=='1':
        if i==0 or j==0:
            dp[i][j]=1

```
class Solution:
    def maximalSquare(self, matrix: List[List[str]]) -> int:
        if len(matrix)==0 or len(matrix[0])==0:
            return 0
        maxSide=0
        rows,columns=len(matrix),len(matrix[0])
        dp=[[0]*columns for _ in range(rows)]
        for i in range(rows):
            for j in range(columns):
                if matrix[i][j]=='1':
                    if i==0 or j==0:
                        dp[i][j]=1
                    else:
                        dp[i][j]=min(dp[i-1][j],dp[i][j-1],dp[i-1][j-1])+1
                    maxSide=max(maxSide,dp[i][j])
        maxSquare=maxSide*maxSide
        return maxSquare

```

### 264. 丑数 II
    链接：https://leetcode-cn.com/problems/ugly-number-ii/

    编写一个程序，找出第 n 个丑数。

    丑数就是质因数只包含 2, 3, 5 的正整数。

    示例:

    输入: n = 10
    输出: 12
    解释: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 是前 10 个丑数。
    说明:  

    1 是丑数。
    n 不超过1690。

题解一|动态规划+3个指针:

```
class Solution:
    def nthUglyNumber(self, n: int) -> int:
        nums=[1]
        i2=i3=i5=0
        for i in range(1,n):
            ugly=min(nums[i2]*2,nums[i3]*3,nums[i5]*5)
            nums.append(ugly)
            if ugly == nums[i2]*2:
                i2+=1
            if ugly == nums[i3]*3:
                i3+=1
            if ugly== nums[i5]*5:
                i5+=1
        return nums[n-1]
```

### 279. 完全平方数
    链接：https://leetcode-cn.com/problems/perfect-squares/

    给定正整数 n，找到若干个完全平方数（比如 1, 4, 9, 16, ...）使得它们的和等于 n。你需要让组成和的完全平方数的个数最少。

    示例 1:

    输入: n = 12
    输出: 3 
    解释: 12 = 4 + 4 + 4.
    示例 2:

    输入: n = 13
    输出: 2
    解释: 13 = 4 + 9.

题解一|动态规划：

1、定义数组元素的含义

    dp[i]表示i最少可以由几个平方数构成

2、寻找递推表达式

    遍历dp，对于i，遍历区间[2,n+1)：
        遍历所有平方数小于i的数j，遍历区间[1,int(\sqrt{i})+1):
            dp[i]=min(dp[i],dp[i-j*j]+1)
            始终保存所有可能情况中的最小值
    返回dp[n]

3、找出初始值
    dp=[0,1,2,...,n]，长度为n+1，最多次数就是全由1构成。

时间复杂度：O(n\sqrt{n})
空间复杂度：O(n)

```
import math
class Solution:
    def numSquares(self, n: int) -> int:
        dp=[i for i in range(n+1)]
        for i in range(2,n+1):
            for j in range(1,int(math.sqrt(i))+1): # 开方也可使用i**0.5
                dp[i]=min(dp[i],dp[i-j*j]+1)
        return dp[n]
```

题解二|广度优先遍历bfs：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/bfs.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/bfs.png)

借助队列实现广度优先遍历（层次遍历）:

    1、初始化队列queue=[n]，访问元组visited={}，初试化路径长度step=0
    2、特判，若n==0，返回0。
    3、循环条件，队列不为空：

        step+=1，因为循环一次，意味着一层中的节点已经遍历完，所以路径长度需要加一。
        定义当前层中的节点数length=len(queue)，遍历当前层的所有节点：
            令tmp为队首元素。
            遍历所有可能数i的平方数，遍历区间[1,int(\sqrt{tmp})+1)：
                定义x=tmp-i^2
                若x==0，返回当前的路径长度。
                若x not in visited，表示当前节点未出现过：将该节点入队并在访问数组中加入。
    4、返回step

时间复杂度：O(n*\sqrt{n})，并不准确
空间复杂度：O(n)

```
from collections import deque
class Solution:
    def numSquares(self, n: int) -> int:
        if n==0:
            return 0
        queue=deque([n])
        visited=set()
        step=0
        while queue:
            step+=1
            length=len(queue)
            for _ in range(length):
                tmp=queue.pop()
                for j in range(1,int(tmp**0.5)+1):
                    node=tmp-j**2
                    if node==0:
                        return step
                    if node not in visited:
                        queue.appendleft(node)
                        visited.add(node)
        return step
```
### 继续理解 300. 最长上升子序列
    链接：https://leetcode-cn.com/problems/longest-increasing-subsequence/

    给定一个无序的整数数组，找到其中最长上升子序列的长度。

    示例:

    输入: [10,9,2,5,3,7,101,18]
    输出: 4 
    解释: 最长的上升子序列是 [2,3,7,101]，它的长度是 4。
    说明:

    可能会有多种最长上升子序列的组合，你只需要输出对应的长度即可。
    你算法的时间复杂度应该为 O(n^2) 。
    进阶: 你能将算法的时间复杂度降低到 O(n*logn) 吗?

题解一|动态规划：

1、定义数组元素的含义

    定义 dp[i] 为考虑前 i 个元素，以第 i 个数字结尾的最长上升子序列的长度，注意 nums[i] 必须被选取。

2、寻找递推表达式

    从小到大计算 dp[] 数组的值，在计算 dp[i] 之前，我们已经计算出 dp[0…i−1] 的值:
    dp[i]=max(dp[j])+1,其中0≤j<i且num[j]<num[i]

3、找出初始值
    dp[i]=1，1 个字符显然是长度为 1 的上升子序列。

时间复杂度：O(n^2)，其中 n 为数组 nums 的长度。动态规划的状态数为 n，计算状态 dp[i] 时，需要 O(n) 的时间遍历 dp[0…i−1] 的所有状态，所以总时间复杂度为 O(n^2)

空间复杂度：O(n)，需要额外使用长度为 n 的 dp 数组。

```
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        if not nums:
            return 0
        dp=[]
        for i in range(len(nums)):
            dp.append(1)
            for j in range(i):
                if nums[i]>nums[j]:
                    dp[i]=max(dp[i],dp[j]+1)
        return max(dp)
```

题解二|动态规划+二分查找：

1、定义数组元素的含义

    tails[k] 的值代表 长度为 k+1 子序列 的尾部元素值。

2、寻找递推表达式

    设 res 为 tails 当前长度，代表直到当前的最长上升子序列长度。设 j∈[0,res)，考虑每轮遍历 nums[k] 时，通过二分法遍历 [0,res) 列表区间，找出 nums[k]的大小分界点，会出现两种情况：

        区间中存在 tails[i] > nums[k] ： 将第一个满足 tails[i] > nums[k]执行 tails[i] = nums[k] ；因为更小的 nums[k]后更可能接一个比它大的数字。
        
        区间中不存在 tails[i] > nums[k] ： 意味着 nums[k]可以接在前面所有长度的子序列之后，因此肯定是接到最长的后面（长度为 res），新子序列长度为 res + 1。

3、找出初始值
    tails[i]=0，tails 列表所有值 =0。

4、返回 res ，即最长上升子子序列长度。

时间复杂度 O(NlogN) ： 遍历 nums 列表需 O(N)，在每个 nums[i] 二分法需 O(logN)
空间复杂度 O(N) ： tails 列表占用线性大小额外空间。

```
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        if not nums:
            return 0
        tails,res=[0]*len(nums),0
        for num in nums:
            i,j=0,res
            while i<j:
                m=(i+j)//2
                if tails[m]<num:
                    i=m+1
                else:
                    j=m
            tails[i]=num
            if j==res:
                res+=1
        return res
```


https://mp.weixin.qq.com/s/02o_OPgePjaz3dXnw9TA1w

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/search.jpg](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/search.jpg)

```
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        n=len(nums)
        top=n*[0]
        piles=0
        for i in range(n):
            poker=nums[i]
            left,right=0,piles
            while left<right:
                mid=left+(right-left)//2
                if top[mid]>poker:
                    right=mid
                elif top[mid]<poker:
                    left=mid+1
                else:
                    right=mid

            if left==piles:
                piles+=1
            top[left]=poker
        return piles
```

题解三|贪心|二分查找：

时间复杂度：O(nlogn)。数组 nums 的长度为 n，我们依次用数组中的元素去更新 dp 数组，而更新 do 数组时需要进行 O(logn) 的二分搜索，所以总时间复杂度为 O(nlogn)。

空间复杂度：O(n)

```
class Solution(object):
    def lengthOfLIS(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if not nums:
            return 0
        dp=[]
        for num in nums:
            if not dp or num>dp[-1]:
                dp.append(num)
            else:
                left,right=0,len(dp)-1
                loc=right
                while left <= right:
                    mid=left+(right-left)//2
                    if dp[mid] >= num:
                        loc=mid
                        right=mid-1
                    else:
                        left=mid+1
                dp[loc]=num
        return len(dp)
```

### 303. 区域和检索 - 数组不可变
    链接：https://leetcode-cn.com/problems/range-sum-query-immutable/

    给定一个整数数组  nums，求出数组从索引 i 到 j  (i ≤ j) 范围内元素的总和，包含 i,  j 两点。

    示例：

    给定 nums = [-2, 0, 3, -5, 2, -1]，求和函数为 sumRange()

    sumRange(0, 2) -> 1
    sumRange(2, 5) -> -1
    sumRange(0, 5) -> -3
    说明:

    你可以假设数组不可变。
    会多次调用 sumRange 方法。

题解一|暴力：
    
    缺点：每次调用sumRange都要重新进行计算

```
class NumArray:

    def __init__(self, nums: List[int]):
        self.nums=nums

    def sumRange(self, i: int, j: int) -> int:
        sum=0
        for each in range(i,j+1):
            sum+=self.nums[each]
        return sum

# Your NumArray object will be instantiated and called as such:
# obj = NumArray(nums)
# param_1 = obj.sumRange(i,j)
```

题解二|缓存+动态规划：
```
class NumArray:

    def __init__(self, nums: List[int]):
        self.nums=nums
        if not nums:
            return
        n=len(nums)
        self.dp=[0]*(n+1)
        self.dp[1]=nums[0]
        for i in range(2,n+1):
            self.dp[i]=self.dp[i-1]+nums[i-1]

    def sumRange(self, i: int, j: int) -> int:
        return self.dp[j+1]-self.dp[i]

# Your NumArray object will be instantiated and called as such:
# obj = NumArray(nums)
# param_1 = obj.sumRange(i,j)
```

```
class NumArray:

    def __init__(self, nums: List[int]):
        self.nums=nums
        if not nums:
            return
        n=len(nums)
        self.dp=[0]*(n+1)
        for i in range(0,n):
            self.dp[i+1]=self.dp[i]+nums[i]
        # print(self.dp)

    def sumRange(self, i: int, j: int) -> int:
        return self.dp[j+1]-self.dp[i]

# Your NumArray object will be instantiated and called as such:
# obj = NumArray(nums)
# param_1 = obj.sumRange(i,j)
```

### 304. 二维区域和检索 - 矩阵不可变
    链接：https://leetcode-cn.com/problems/range-sum-query-2d-immutable/

    给定一个二维矩阵，计算其子矩形范围内元素的总和，该子矩阵的左上角为 (row1, col1) ，右下角为 (row2, col2)。

    Range Sum Query 2D
    上图子矩阵左上角 (row1, col1) = (2, 1) ，右下角(row2, col2) = (4, 3)，该子矩形内元素的总和为 8。

    示例:

    给定 matrix = [
      [3, 0, 1, 4, 2],
      [5, 6, 3, 2, 1],
      [1, 2, 0, 1, 5],
      [4, 1, 0, 1, 7],
      [1, 0, 3, 0, 5]
    ]

    sumRegion(2, 1, 4, 3) -> 8
    sumRegion(1, 1, 2, 2) -> 11
    sumRegion(1, 2, 2, 4) -> 12
    说明:

    你可以假设矩阵不可变。
    会多次调用 sumRegion 方法。
    你可以假设 row1 ≤ row2 且 col1 ≤ col2。

题解一|动态规划：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/dp22.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/dp22.png)

    sum(abcd)=sum(od)−sum(ob)−sum(oc)+sum(oa)

1、定义数组的含义
    
    dp[i][j]表示矩阵左上角到matrix[i][j]的累加和


```
class NumMatrix:

    def __init__(self, matrix: List[List[int]]):
        
        if not matrix or not matrix[0]:pass
        else:
            row = len(matrix)
            col = len(matrix[0])
            self.dp = [[ 0 ] * (col + 1) for _ in range(row + 1)]
            # 求行的前缀和
            for i in range(1, row + 1):
                for j in range(1 ,col + 1):
                    self.dp[i][j] = self.dp[i][j - 1] + matrix[i - 1][j - 1] 
            # 求列的前缀和
            for j in range(1, col + 1):
                for i in range(1, row + 1):
                    self.dp[i][j] += self.dp[i - 1][j]
            # print(self.dp)

    def sumRegion(self, row1: int, col1: int, row2: int, col2: int) -> int:
        return self.dp[row2 + 1][col2 + 1] - self.dp[row1][col2 + 1] - self.dp[row2 + 1][col1] + self.dp[row1][col1]

# Your NumMatrix object will be instantiated and called as such:
# obj = NumMatrix(matrix)
# param_1 = obj.sumRegion(row1,col1,row2,col2)
```

```
class NumMatrix:

    def __init__(self, matrix: List[List[int]]):
        
        if not matrix or not matrix[0]:pass
        else:
            row = len(matrix)
            col = len(matrix[0])
            self.dp = [[ 0 ] * (col + 1) for _ in range(row + 1)]
        
            for i in range(1, row + 1):
                for j in range(1, col + 1):
                    self.dp[i][j] = matrix[i - 1][j - 1] + self.dp[i - 1][j] + self.dp[i][j - 1] - self.dp[i-1][j-1] # [i-1, j-1] # 重复计算了, 所以需要减去.

    def sumRegion(self, row1: int, col1: int, row2: int, col2: int) -> int:
        return self.dp[row2 + 1][col2 + 1] - self.dp[row1][col2 + 1] - self.dp[row2 + 1][col1] + self.dp[row1][col1]

# Your NumMatrix object will be instantiated and called as such:
# obj = NumMatrix(matrix)
# param_1 = obj.sumRegion(row1,col1,row2,col2)
```


### 334. 递增的三元子序列
    链接：https://leetcode-cn.com/problems/increasing-triplet-subsequence/
    
    给定一个未排序的数组，判断这个数组中是否存在长度为 3 的递增子序列。

    数学表达式如下:

    如果存在这样的 i, j, k,  且满足 0 ≤ i < j < k ≤ n-1，
    使得 arr[i] < arr[j] < arr[k] ，返回 true ; 否则返回 false 。
    说明: 要求算法的时间复杂度为 O(n)，空间复杂度为 O(1) 。

    示例 1:

    输入: [1,2,3,4,5]
    输出: true
    示例 2:

    输入: [5,4,3,2,1]
    输出: false

题解一|动态规划：

时间复杂度：O(n^2)
空间复杂度：O(n)

```
class Solution:
    def increasingTriplet(self, nums: List[int]) -> bool:
        n=len(nums)
        dp=[1]*n
        for i in range(1,n):
            for j in range(i):
                if nums[j]<nums[i]:
                    dp[i]=max(dp[i],dp[j]+1)
                if dp[i]>=3:
                    return True
        return False

```

题解二|双指针：

时间复杂度：O(n)
空间复杂度：O(1)

```
class Solution:
    def increasingTriplet(self, nums: List[int]) -> bool:
        m1,m2=float('inf'),float('inf')
        for i in nums:
            if m1>=i:
                m1=i
            elif m2>=i:
                m2=i
            else:
                return True
        return False
```

### 338. 比特位计数
    链接：https://leetcode-cn.com/problems/counting-bits/

    给定一个非负整数 num。对于 0 ≤ i ≤ num 范围中的每个数字 i ，计算其二进制数中的 1 的数目并将它们作为数组返回。

    示例 1:

    输入: 2
    输出: [0,1,1]
    示例 2:

    输入: 5
    输出: [0,1,1,2,1,2]
    进阶:

    给出时间复杂度为O(n*sizeof(integer))的解答非常容易。但你可以在线性时间O(n)内用一趟扫描做到吗？
    要求算法的空间复杂度为O(n)。
    你能进一步完善解法吗？要求在C++或任何其他语言中不使用任何内置函数（如 C++ 中的 __builtin_popcount）来执行此操作。

题解一|动态规划：

1、定义数组含义

    dp[i]表示数字i计算其二进制数中的 1 的数目。

2、找出数组递推关系式

    当i为奇数时：
        奇数一定比前面那个偶数多一个 1，因为多的就是最低位的 1。
    当i为偶数时：
        二进制是逢2开始进位的，也就是在偶数的时候，一个偶数相当一另一个数成以2得到，在二进制运算中，就是左移一位，也就是在低位多加1个0，故遇到偶数的时候就是dp[i] = dp[i/2]。

3、找到初始值

    dp[0]=0
    dp[1]=1

时间复杂度：O(n)
空间复杂度：O(n)

```
class Solution:
    def countBits(self, num: int) -> List[int]:
        if num==0:
            return [0]
        dp=[0]*(num+1)
        dp[0]=0
        dp[1]=1
        for i in range(2,num+1):
            if i%2==0:
                dp[i]=dp[i//2]
            else:
                dp[i]=dp[i-1]+1
        return dp
```

### 343. 整数拆分
    链接：https://leetcode-cn.com/problems/integer-break/

    给定一个正整数 n，将其拆分为至少两个正整数的和，并使这些整数的乘积最大化。 返回你可以获得的最大乘积。

    示例 1:

    输入: 2
    输出: 1
    解释: 2 = 1 + 1, 1 × 1 = 1。
    示例 2:

    输入: 10
    输出: 36
    解释: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36。
    说明: 你可以假设 n 不小于 2 且不大于 58。

题解一|动态规划

1、定义数组
    
    dp[i]代表i拆分后的最大乘积
2、递推表达式

    dp[i]=max(dp[i],j*(i-j),j*dp[i-j])

    (i - j) * j 代表的是整数 i 拆分成 2 部分, 分别为 (i - j) * j；

    j * dp[i - j]代表的是整数 i 拆 2 部分以上， dp[i - j] 就是代表在之前的动态规划求得的整数 i - j 的分割最大的乘积

3、初始值
    dp[i]=1，从3开始。

```
class Solution:
    def integerBreak(self, n: int) -> int:
        dp=[1 for i in range(n+1)]
        for i in range(3,n+1):
            for j in range(1,i):
                dp[i]=max(dp[i],j*(i-j),j*dp[i-j])
        return dp[n]
```

### 354. 俄罗斯套娃信封问题
    链接：https://leetcode-cn.com/problems/russian-doll-envelopes/

    给定一些标记了宽度和高度的信封，宽度和高度以整数对形式 (w, h) 出现。当另一个信封的宽度和高度都比这个信封大的时候，这个信封就可以放进另一个信封里，如同俄罗斯套娃一样。

    请计算最多能有多少个信封能组成一组“俄罗斯套娃”信封（即可以把一个信封放到另一个信封里面）。

    说明:
    不允许旋转信封。

    示例:

    输入: envelopes = [[5,4],[6,4],[6,7],[2,3]]
    输出: 3 
    解释: 最多信封的个数为 3, 组合为: [2,3] => [5,4] => [6,7]。

题解一|动态规划|超时
```
class Solution:
    def maxEnvelopes(self, envelopes: List[List[int]]) -> int:
        if not envelopes:
            return 0
        envelopes=sorted(envelopes,key=lambda x:(x[0],-x[1]))
        dp=[1]*len(envelopes)
        count=1
        for i in range(len(envelopes)):
            for j in range(i):
                if envelopes[i][1] > envelopes[j][1] and dp[j]+1 > dp[i]:
                    dp[i]=dp[j]+1
                count=max(count,dp[i])
        return count
```

稍微修改下，通过：

```
class Solution:
    def maxEnvelopes(self, envelopes: List[List[int]]) -> int:
        if not envelopes:
            return 0
        envelopes=sorted(envelopes,key=lambda x:(x[0],-x[1]))
        dp=[1]*len(envelopes)
        for i in range(len(envelopes)):
            for j in range(i):
                if envelopes[i][1] > envelopes[j][1] and dp[j]+1 > dp[i]:
                    dp[i]=max(dp[i],dp[j]+1)
        return max(dp)
```

### 357. 计算各个位数不同的数字个数
    链接：https://leetcode-cn.com/problems/count-numbers-with-unique-digits/

    给定一个非负整数 n，计算各位数字都不同的数字 x 的个数，其中 0 ≤ x < 10^n 。

    示例:

    输入: 2
    输出: 91 
    解释: 答案应为除去 11,22,33,44,55,66,77,88,99 外，在 [0,100) 区间内的所有数字。

题解一|动态规划:
    
    数字一共有0~9十个，所以n超过十位一定会重复。

    1位 10
    2位 9*9+10 （第一位不能0）
    3位 9*9*8+9*9+10
    4位 9*9*8*7+9*9*8+9*9+10
    ...
    i位 dp1[i] = dp2[i-1]*(10-(i-1))+dp1[i-1]  （i从2开始，dp1[1] = 10, dp2[1] = 9）


```
class Solution:
    def countNumbersWithUniqueDigits(self, n: int) -> int:
        if n==0:
            return 1
        dp1=[0]*(n+1)
        dp2=[0]*(n+1)
        dp1[1]=10
        dp2[1]=9
        for i in range(2,min(11,n+1)):
            dp2[i]=dp2[i-1]*(10-(i-1))
            dp1[i]=dp2[i]+dp1[i-1]
        if n>=11:
            return dp[10]
        return dp1[n]
```

```
class Solution:
    def countNumbersWithUniqueDigits(self, n: int) -> int:
        if n==0:
            return 1
        fisrt,second=10,9*9
        size=min(n,10)
        for i in range(2,size+1):
            fisrt+=second
            second*=(10-i)
        return fisrt
```

??题解二|回溯：
```
class Solution:
    def countNumbersWithUniqueDigits(self, n: int) -> int:
        used=[False]*10
        def backtrace(first,used):
            count=0
            if first != n:
                for i in range(10):
                    # 剪枝：多位数时，第一个数字不能为0
                    if i==0 and n>1 and first==1:
                        continue
                    if used[i]:
                        continue
                    used[i]=True
                    count+=backtrace(first+1,used)+1
                    used[i]=False
            return count
        if not n:
            return 1
        return backtrace(0,used)
```

### 368. 最大整除子集
    链接：https://leetcode-cn.com/problems/largest-divisible-subset/

    给出一个由无重复的正整数组成的集合，找出其中最大的整除子集，子集中任意一对 (Si，Sj) 都要满足：Si % Sj = 0 或 Sj % Si = 0。

    如果有多个目标子集，返回其中任何一个均可。

    示例 1:

    输入: [1,2,3]
    输出: [1,2] (当然, [1,3] 也正确)
    示例 2:

    输入: [1,2,4,8]
    输出: [1,2,4,8]

题解一|动态规划：

1、sorted()排序；
2、dp[i]表示以下标i位置元素结尾得最长序列。
3、遍历0-(i-1)，如果nums[i]整除nums[j]，则判断dp[i]和dp[j] + 1哪个大

```
class Solution:
    def largestDivisibleSubset(self, nums: List[int]) -> List[int]:
        if not nums:
            return []
        nums=sorted(nums)
        dp=[[i] for i in nums]
        res=[]
        for i in range(0,len(nums)):
            for j in range(0,i):
                if nums[i]%nums[j]==0:
                    if len(dp[i])<len(dp[j]+[nums[i]]):
                        dp[i]=dp[j]+[nums[i]]
            if len(dp[i])>len(res):
                res=dp[i]
        return res
```

### 392. 判断子序列
    链接：https://leetcode-cn.com/problems/is-subsequence/

    给定字符串 s 和 t ，判断 s 是否为 t 的子序列。

    你可以认为 s 和 t 中仅包含英文小写字母。字符串 t 可能会很长（长度 ~= 500,000），而 s 是个短字符串（长度 <=100）。

    字符串的一个子序列是原始字符串删除一些（也可以不删除）字符而不改变剩余字符相对位置形成的新字符串。（例如，"ace"是"abcde"的一个子序列，而"aec"不是）。

    示例 1:
    s = "abc", t = "ahbgdc"

    返回 true.

    示例 2:
    s = "axc", t = "ahbgdc"

    返回 false.

    后续挑战 :

    如果有大量输入的 S，称作S1, S2, ... , Sk 其中 k >= 10亿，你需要依次检查它们是否为 T 的子序列。在这种情况下，你会怎样改变代码？

题解一|双指针：
```
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        p,q=0,0
        count=0
        while p<len(s) and q<len(t):
            if s[p]==t[q]:
                count+=1
                p+=1
            q+=1
        return p==len(s)
```

题解二|二分+哈希：
```
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        hash={}
        for i,word in enumerate(t):
            if word not in hash:
                hash[word]=[i] # 注意这里是列表
            else:
                hash[word].append(i)

        index = -1
        for word in s:
            if word not in hash:
                return False
            # 字母s出现的索引 用二分法找到其中大于index的第一个
            indexes = hash[word]
            left = 0
            right = len(indexes)
            while left < right:
                mid = left + (right - left) // 2
                if indexes[mid] > index:
                    right = mid
                else:
                    left = mid + 1
            if left == len(indexes):
                return False
            index = indexes[left]
            
        return True
```

### 516. 最长回文子序列
    给定一个字符串 s ，找到其中最长的回文子序列，并返回该序列的长度。可以假设 s 的最大长度为 1000 。

    示例 1:
    输入:

    "bbbab"
    输出:

    4
    一个可能的最长回文子序列为 "bbbb"。

    示例 2:
    输入:

    "cbbd"
    输出:

    2
    一个可能的最长回文子序列为 "bb"。

    提示：

    1 <= s.length <= 1000
    s 只包含小写英文字母

题解一|动态规划：

1、dp 数组的定义是：在子串s[i..j]中，最长回文子序列的长度为dp[i][j]

2、状态转移方程
```
if (s[i] == s[j])
    // 它俩一定在最长回文子序列中
    dp[i][j] = dp[i + 1][j - 1] + 2;
else
    // s[i+1..j] 和 s[i..j-1] 谁的回文子序列更长？
    dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]);
```

答案：dp[0][n - 1]，也就是整个s的最长回文子序列的长度。

3、bad case

如果只有一个字符，显然最长回文子序列长度是 1，也就是dp[i][j] = 1,(i == j)。

因为i肯定小于等于j，所以对于那些i > j的位置，根本不存在什么子序列，应该初始化为 0。


为了保证每次计算dp[i][j]，左、下、左下三个方向的位置已经被计算出来，只能斜着遍历或者反着遍历：

```
class Solution:
    def longestPalindromeSubseq(self, s: str) -> int:
        n=len(s)
        dp=[[0]*n for i in range(n)]

        for i in range(n):
            dp[i][i]=1

        # 反着遍历保证正确的状态转移
        for i in range(n-1,-1,-1):
            for j in range(i+1,n):
                if s[i] == s[j]:
                    dp[i][j]=dp[i+1][j-1]+2
                else:
                    dp[i][j]=max(dp[i+1][j],dp[i][j-1])
        return dp[0][n-1]

```

### ???651.4键键盘
![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/keyborad.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/keyborad.png)

如何在 N 次敲击按钮后得到最多的 A？

对 dp 数组的不同定义需要完全不同的逻辑，从而产生完全不同的解法。

题解一：

流程：对于动态规划问题，首先要明白有哪些「状态」，有哪些「选择」。

有哪些「选择」是很明显的：

    4 种，就是题目中提到的四个按键，分别是A、C-A、C-C、C-V（Ctrl简写为C）。

有哪些「状态」？或者换句话说，我们需要知道什么信息，才能将原问题分解为规模更小的子问题？

定义三个状态行不行：

    第一个状态是剩余的按键次数，用n表示；
    第二个状态是当前屏幕上字符 A 的数量，用a_num表示；
    第三个状态是剪切板中字符 A 的数量，用copy表示。

如此定义「状态」，就可以知道 base case：当剩余次数n为 0 时，a_num就是我们想要的答案。

结合刚才说的 4 种「选择」，我们可以把这几种选择通过状态转移表示出来：

    dp(n - 1, a_num + 1, copy),    # A
    解释：按下 A 键，屏幕上加一个字符
    同时消耗 1 个操作数

    dp(n - 1, a_num + copy, copy), # C-V
    解释：按下 C-V 粘贴，剪切板中的字符加入屏幕
    同时消耗 1 个操作数

    dp(n - 2, a_num, a_num)        # C-A C-C
    解释：全选和复制必然是联合使用的，
    剪切板中 A 的数量变为屏幕上 A 的数量
    同时消耗 2 个操作数

问题的规模n在不断减小，肯定可以到达n = 0的 base case，所以这个思路是正确的：

```
def maxA(n):
    def dp(n,a_num,copy):
        # 初始值
        if n<=0:
            return a_num
        # 所有选择全部试一遍，选择最大结果
        return max(
            dp(n-1,a_num+1,copy),
            dp(n-1,a_num+copy,copy),
            dp(n-2,a_num,a_num)
            )
    return dp(n,0,0) # 可以按n次按键，屏幕和剪切板种还没有A
```

优化：

```
def maxA(n):
    memo=dict()
    def dp(n,a_num,copy):
        # 初始值
        if n<=0:
            return a_num
        # 避免计算重叠子问题
        if (n,a_num,copy) in demo:
            return memo[(n,a_num,copy)]

        memo[(n,a_num,copy)]= max(
            dp(n-1,a_num+1,copy),
            dp(n-1,a_num+copy,copy),
            dp(n-2,a_num,a_num)
            )
        return memo[(n,a_num,copy)]
    return dp(n,0,0) # 可以按n次按键，屏幕和剪切板种还没有A
```

可以把这个 dp 函数写成 dp 数组：

    dp[n][a_num][copy] #状态的总数（时空复杂度）就是这个三维数组的体积

我们知道变量n最多为N，但是a_num和copy最多为多少我们很难计算，复杂度起码也有 O(N^3) 吧。

题解二：

「选择」还是那 4 个，但是这次我们只定义一个「状态」，也就是剩余的敲击次数n。

最优按键序列一定只有两种情况：

    要么一直按A：A,A,…A（当 N 比较小时）。

    要么是这么一个形式：A,A,…C-A,C-C,C-V,C-V,…C-V（当 N 比较大时）。

因为字符数量少（N 比较小）时，C-A C-C C-V这一套操作的代价相对比较高，可能不如一个个按A；而当 N 比较大时，后期C-V的收获肯定很大。这种情况下整个操作序列大致是：开头连按几个A，然后C-A C-C组合再接若干C-V，然后再C-A C-C接着若干C-V，循环下去。

换句话说，最后一次按键要么是A要么是C-V。明确了这一点，可以通过这两种情况来设计算法：

https://mp.weixin.qq.com/s?__biz=Mzg2NzA4MTkxNQ==&mid=2247486045&idx=2&sn=c270733b9ea06403ab9851bbc5d935db&scene=21#wechat_redirect

### 673. 最长递增子序列的个数
    链接：https://leetcode-cn.com/problems/number-of-longest-increasing-subsequence/

    给定一个未排序的整数数组，找到最长递增子序列的个数。

    示例 1:

    输入: [1,3,5,4,7]
    输出: 2
    解释: 有两个最长递增子序列，分别是 [1, 3, 4, 7] 和[1, 3, 5, 7]。
    示例 2:

    输入: [2,2,2,2,2]
    输出: 5
    解释: 最长递增子序列的长度是1，并且存在5个子序列的长度为1，因此输出5。

题解一|动态规划：
```
class Solution:
    def findNumberOfLIS(self, nums: List[int]) -> int:
        if not nums:
            return 0
        dp=[1]*len(nums)
        count=[1]*len(nums)
        MAX=0
        for i in range(len(nums)):
            for j in range(i):
                if nums[i] > nums[j]:
                    if dp[j]+1 > dp[i]: # 代表第一次遇到最长子序列
                        dp[i]=max(dp[i],dp[j]+1)
                        count[i]=count[j]
                    elif dp[j]+1 == dp[i]: # 代表已经遇到最长子序列
                        count[i]+=count[j]
        MAX = max(dp)
        res=0
        for i in range(len(nums)):
            if dp[i]==MAX:
                res+=count[i]
        return res
```

### 674. 最长连续递增序列
    链接：https://leetcode-cn.com/problems/longest-continuous-increasing-subsequence/

    给定一个未经排序的整数数组，找到最长且连续的的递增序列，并返回该序列的长度。

    示例 1:

    输入: [1,3,5,4,7]
    输出: 3
    解释: 最长连续递增序列是 [1,3,5], 长度为3。
    尽管 [1,3,5,7] 也是升序的子序列, 但它不是连续的，因为5和7在原数组里被4隔开。 
    示例 2:

    输入: [2,2,2,2,2]
    输出: 1
    解释: 最长连续递增序列是 [2], 长度为1。

题解一|动态规划：

1、数组定义：dp[i]代表nums[i]值结尾的最长连续递增序列的长度
2、状态转移方程

    nums[i]＞nums[i−1]，i至少可以与i-1形成一个连续递增序列，因为它们俩挨着，并且是递增的，长度上是dp[i-1]+1

    nums[i]＜=nums[i-1],这时候，不能形成连续递增序列，后一个数要比前一个数小，呈下降的趋势，注意不认为是递增的

3、初始值dp[i]=1

时间复杂度：o(n)
空间复杂度：o(n)
```
class Solution:
    def findLengthOfLCIS(self, nums: List[int]) -> int:
        if not nums:
            return 0
        dp=[1]*len(nums)
        for i in range(1,len(nums)):
                if nums[i] > nums[i-1]:
                    dp[i]=max(dp[i],dp[i-1]+1)
        return max(dp)                    
```

o(1)

```
public int findLengthOfLCIS(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        int n = nums.length;
        int max = 1;
        int[] dp = new int[2];
        dp[0] = 1;
        for (int i = 1; i ＜ n; i++) {
            dp[i % 2] = 1;//前一个状态值都会被覆盖，需要重新初始化
            if (nums[i] ＞ nums[i - 1]) {
                dp[i % 2] += dp[(i - 1) % 2];//当前状态依赖前一状态，需要再前一状态上累加
            }
            max = Math.max(max, dp[i % 2]);
        }
        return max;
    }
           
```

### 746. 使用最小花费爬楼梯
    链接：https://leetcode-cn.com/problems/min-cost-climbing-stairs/

    数组的每个索引作为一个阶梯，第 i个阶梯对应着一个非负数的体力花费值 cost[i](索引从0开始)。

    每当你爬上一个阶梯你都要花费对应的体力花费值，然后你可以选择继续爬一个阶梯或者爬两个阶梯。

    您需要找到达到楼层顶部的最低花费。在开始时，你可以选择从索引为 0 或 1 的元素作为初始阶梯。

    示例 1:

    输入: cost = [10, 15, 20]
    输出: 15
    解释: 最低花费是从cost[1]开始，然后走两步即可到阶梯顶，一共花费15。
     示例 2:

    输入: cost = [1, 100, 1, 1, 1, 100, 1, 1, 100, 1]
    输出: 6
    解释: 最低花费方式是从cost[0]开始，逐个经过那些1，跳过cost[3]，一共花费6。
    注意：

    cost 的长度将会在 [2, 1000]。
    每一个 cost[i] 将会是一个Integer类型，范围为 [0, 999]。

题解一|动态规划：
1、定义
    dp[i]为踏上第i级台阶的最小花费
2、状态转移
踏上第i级台阶有两种方法：

    先踏上第i-2级台阶（最小总花费dp[i-2]），再直接迈两步踏上第i级台阶（花费cost[i]），最小总花费dp[i-2] + cost[i]；

    先踏上第i-1级台阶（最小总花费dp[i-1]），再迈一步踏上第i级台阶（花费cost[i]），最小总花费dp[i-1] + cost[i]；

    则dp[i]是上面这两个最小总花费中的最小值。

因此状态转移方程是：dp[i] = min(dp[i-2], dp[i-1]) + cost[i]。

3、初始条件：

    最后一步踏上第0级台阶，最小花费dp[0] = cost[0]。

    最后一步踏上第1级台阶有两个选择：

    可以分别踏上第0级与第1级台阶，花费cost[0] + cost[1]；
    也可以从地面开始迈两步直接踏上第1级台阶，花费cost[1]。
    最小值dp[1] = min(cost[0] + cost[1], cost[1]) = cost[1]。

```
class Solution:
    def minCostClimbingStairs(self, cost: List[int]) -> int:
        if not cost:
            return 0
        dp=[0]*len(cost)
        dp[0]=cost[0]
        dp[1]=cost[1]
        for i in range(2,len(cost)):
            dp[i]=min(dp[i-1]+cost[i],dp[i-2]+cost[i])
        return min(dp[-1],dp[-2])
```

### 1025. 除数博弈
    链接：https://leetcode-cn.com/problems/divisor-game/

    爱丽丝和鲍勃一起玩游戏，他们轮流行动。爱丽丝先手开局。

    最初，黑板上有一个数字 N 。在每个玩家的回合，玩家需要执行以下操作：

    选出任一 x，满足 0 < x < N 且 N % x == 0 。
    用 N - x 替换黑板上的数字 N 。
    如果玩家无法执行这些操作，就会输掉游戏。

    只有在爱丽丝在游戏中取得胜利时才返回 True，否则返回 false。假设两个玩家都以最佳状态参与游戏。

    示例 1：

    输入：2
    输出：true
    解释：爱丽丝选择 1，鲍勃无法进行操作。
    示例 2：

    输入：3
    输出：false
    解释：爱丽丝选择 1，鲍勃也选择 1，然后爱丽丝无法进行操作。
     

    提示：

    1 <= N <= 1000

题解一|归纳

思路：最后轮到谁时 N=1，那么谁就是输家。

假设N为奇数，即 N%2=1:

    那么 N 的约数x一定都是奇数，N-x 一定是偶数；

假设N为偶数，即 N%2=0:

    那么 N 的约数x可能是奇数，也可能是偶数。x为奇数时，N-x为奇数；x为偶数时，N-x为偶数。

题目中说“两个玩家都以最佳状态参与游戏”，并且爱丽丝先手，那她一定会努力让自己得胜。

结论：

    若N是偶数，爱丽丝一定会选x为奇数，让轮到鲍勃的(N-x)为奇数，这样她每次都会轮到偶数直至胜利；

    若N是奇数，爱丽丝没得选，只能让鲍勃轮到的（N-x)为偶数，此时主动权就在鲍勃手中，他会让爱丽丝每次轮到奇数，直至鲍勃获胜。

```
class Solution:
    def divisorGame(self, N: int) -> bool:
        return N%2==0
```
题解二|动态规划

定义：dp[i]为当N=i时，Alice的输赢

```
class Solution:
    def divisorGame(self, N: int) -> bool:
        if N<=1:
            return False
        dp=[False]* (N+1)
        dp[1]= False
        dp[2]= True
        for i in range(3,N+1):
            for j in range(1,i):
                if dp[i-j] == False and i%j == 0: # 当dp[i-j]为奇数时，dp[i]为偶数
                    dp[i]=True
                    break
        return dp[N]
```

### 面试题 08.01. 三步问题
    链接：https://leetcode-cn.com/problems/three-steps-problem-lcci/

    三步问题。有个小孩正在上楼梯，楼梯有n阶台阶，小孩一次可以上1阶、2阶或3阶。实现一种方法，计算小孩有多少种上楼梯的方式。结果可能很大，你需要对结果模1000000007。

    示例1:

     输入：n = 3 
     输出：4
     说明: 有四种走法
    示例2:

     输入：n = 5
     输出：13
    提示:

    n范围在[1, 1000000]之间

题解一|动态规划
```
class Solution:
    def watiaosToStep(self, n: int) -> int:
        if n < 3:
            return n
        dp=[0]*(n+1)
        dp[1]=1
        dp[2]=2
        dp[3]=4
        for i in range(4,n+1):
            dp[i]=(dp[i-1]+dp[i-2]+dp[i-3]) % 1000000007
        return dp[n]
```

### 面试题 16.17. 连续数列
    链接：https://leetcode-cn.com/problems/contiguous-sequence-lcci/

    给定一个整数数组，找出总和最大的连续数列，并返回总和。

    示例：

    输入： [-2,1,-3,4,-1,2,1,-5,4]
    输出： 6
    解释： 连续子数组 [4,-1,2,1] 的和最大，为 6。
    进阶：

    如果你已经实现复杂度为 O(n) 的解法，尝试使用更为精妙的分治法求解。

```
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        if not nums:
            return 0
        if len(nums)==1:
            return nums[0]
        dp=[0]*len(nums)
        dp[0]=nums[0]
        dp[1]=max(dp[0]+nums[1],nums[1])
        for i in range(2,len(nums)):
            dp[i]=max(dp[i-1]+nums[i],nums[i])
        return max(dp)
```

### 面试题42. 连续子数组的最大和
    链接：https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/

    输入一个整型数组，数组里有正数也有负数。数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。

    要求时间复杂度为O(n)。

    示例1:

    输入: nums = [-2,1,-3,4,-1,2,1,-5,4]
    输出: 6
    解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
     

    提示：

    1 <= arr.length <= 10^5
    -100 <= arr[i] <= 100

```
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        if not nums:
            return 0
        if len(nums)==1:
            return nums[0]
        dp=[0]*len(nums)
        dp[0]=nums[0]
        dp[1]=max(dp[0]+nums[1],nums[1])
        for i in range(2,len(nums)):
            dp[i]=max(dp[i-1]+nums[i],nums[i])
        return max(dp)
```
