---
title: LeetCode-12-字符串
date: 2020-03-28 23:45:50
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---

常用函数：
    
    bin() # 十进制转二进制
    oct() # 十进制转八进制
    hex() # 十进制转十六进制

    chr() # 数字转字符串(ascii)
    ord() # 字符串(ascii)转数字

    65--'A'
    97--'a'

## 十二、字符串

### 5.最长回文子串
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

题解二：

    每个字母当成回文串的中心
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

?题解三：
    
    把每个字母当成回文串的结束

```
class Solution:
    def longestPalindrome(self, s: str) -> str:
        if not s:
            return ""
        max_len = 1
        n = len(s)
        start = 0
        for i in range(1,n):
            even = s[i-max_len:i+1]
            odd = s[i - max_len-1:i+1]
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

参考：https://leetcode-cn.com/problems/longest-palindromic-substring/solution/zhong-xin-kuo-san-dong-tai-gui-hua-by-liweiwei1419/

    1、定义数组的定义
        dp[i][j] 表示子串 s[i, j] 是否为回文子串。
    2、找出数组的关系式
        dp[i][j] = (s[i] == s[j]) and dp[i + 1][j - 1]

        说明：

            「动态规划」事实上是在填一张二维表格，由于构成子串，因此 i 和 j 的关系是 i <= j ，因此，只需要填这张表格对角线以上的部分。

            看到 dp[i + 1][j - 1] 就得考虑边界情况。

        边界条件是：表达式 [i + 1, j - 1] 不构成区间，即长度严格小于 2，即 j - 1 - (i + 1) + 1 < 2 ，整理得 j - i < 3。

        这个结论很显然：j - i < 3 等价于 j - i + 1 < 4，即当子串 s[i..j] 的长度等于 2 或者等于 3 的时候，其实只需要判断一下头尾两个字符是否相等就可以直接下结论了。

            如果子串 s[i + 1..j - 1] 只有 1 个字符，即去掉两头，剩下中间部分只有 11 个字符，显然是回文；
            如果子串 s[i + 1..j - 1] 为空串，那么子串 s[i, j] 一定是回文子串。

        因此，在 s[i] == s[j] 成立和 j - i < 3 的前提下，直接可以下结论，dp[i][j] = true，否则才执行状态转移。

    3、找到初始值
        初始化的时候，单个字符一定是回文串，因此把对角线先初始化为 true，即 dp[i][i] = true 。

        事实上，初始化的部分都可以省去。因为只有一个字符的时候一定是回文，dp[i][i] 根本不会被其它状态值所参考。

    4、考虑输出

        只要一得到 dp[i][j] = true，就记录子串的长度和起始位置，没有必要截取，这是因为截取字符串也要消耗性能，记录此时的回文子串的「起始位置」和「回文长度」即可。

```
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n=len(s)
        if n < 2:
            return s
        dp=[[False]*n for i in range(n)]
        res=1
        start=0

        for i in range(n):
            dp[i][i]=True

        for j in range(1,n):
            for i in range(0,j):
                if s[i]==s[j]:
                    if j-i < 3:
                        dp[i][j]=True
                    else:
                        dp[i][j]=dp[i+1][j-1]
                else:
                    dp[i][j]=False

                if dp[i][j]:
                    cur=j-i+1
                    if cur>res:
                        res=cur
                        start=i
        return s[start:start+res]

```

https://leetcode-cn.com/problems/longest-palindromic-substring/solution/gao-hao-dong-tai-gui-hua-he-zhong-xin-tuo-zhan-zhu/


### 7. 整数反转
    链接：https://leetcode-cn.com/problems/reverse-integer/

    给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

    示例 1:

    输入: 123
    输出: 321
     示例 2:

    输入: -123
    输出: -321
    示例 3:

    输入: 120
    输出: 21
    注意:

    假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−231,  231 − 1]。请根据这个假设，如果反转后整数溢出那么就返回0

题解一：
```
class Solution:
    def reverse(self, x: int) -> int:
        flag=1 if x>0 else -1
        new=abs(x)
        res=0
        while new:
            res=res*10 + new%10
            new//=10
        if res>pow(-2,31) and res<pow(2,31)-1:
            return flag*res
        else:
            return 0
```

题解二：
```
class Solution:
    def reverse(self, x: int) -> int:
        x=int(str(x)[::-1]) if x>=0 else -int(str(-x)[::-1])
        return x if x<pow(2,31)-1 and x>-pow(2,31) else 0
```


### 8. 字符串转换整数 (atoi)

链接：https://leetcode-cn.com/problems/string-to-integer-atoi/

    请你来实现一个 atoi 函数，使其能将字符串转换成整数。

    首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。接下来的转化规则如下：

    如果第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字字符组合起来，形成一个有符号整数。
    假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成一个整数。
    该字符串在有效的整数部分之后也可能会存在多余的字符，那么这些字符可以被忽略，它们对函数不应该造成影响。
    注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换，即无法进行有效转换。

    在任何情况下，若函数不能进行有效的转换时，请返回 0 。

    提示：

    本题中的空白字符只包括空格字符 ' ' 。
    假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 [−231,  231 − 1]。如果数值超过这个范围，请返回  INT_MAX (231 − 1) 或 INT_MIN (−231) 。
     

    示例 1:

    输入: "42"
    输出: 42
    示例 2:

    输入: "   -42"
    输出: -42
    解释: 第一个非空白字符为 '-', 它是一个负号。
         我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。
    示例 3:

    输入: "4193 with words"
    输出: 4193
    解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。
    示例 4:

    输入: "words and 987"
    输出: 0
    解释: 第一个非空字符是 'w', 但它不是数字或正、负号。
         因此无法执行有效的转换。
    示例 5:

    输入: "-91283472332"
    输出: -2147483648
    解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 
         因此返回 INT_MIN (−231) 。

题解一：
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
                print(last)
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

### 12.转罗马数字
    链接：https://leetcode-cn.com/problems/integer-to-roman/

    罗马数字包含以下七种字符： I， V， X， L，C，D 和 M。

    字符          数值
    I             1
    V             5
    X             10
    L             50
    C             100
    D             500
    M             1000
    例如， 罗马数字 2 写做 II ，即为两个并列的 1。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。

    通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：

    I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
    X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
    C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。
    给定一个整数，将其转为罗马数字。输入确保在 1 到 3999 的范围内。

    示例 1:

    输入: 3
    输出: "III"
    示例 2:

    输入: 4
    输出: "IV"
    示例 3:

    输入: 9
    输出: "IX"
    示例 4:

    输入: 58
    输出: "LVIII"
    解释: L = 50, V = 5, III = 3.
    示例 5:

    输入: 1994
    输出: "MCMXCIV"
    解释: M = 1000, CM = 900, XC = 90, IV = 4.

题解一|贪心算法：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/rome.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/rome.png)
```
class Solution:
    def intToRoman(self, num: int) -> str:
        # dicts={'I':1,'V':5,'X':10,'L':50,'C':100,'D':500,'M':1000}

        dicts={1:'I',4:'IV',5:'V',9:'IX',10:'X',40:'XL',50:'L',90:'XC',100:'C',400:'CD',500:'D',900:'CM',1000:'M' }
        ans=''
        for key in sorted(dicts.keys())[::-1]:
            if num==0:
                break
            tmp=num//key
            if tmp==0:
                continue
            ans+=dicts[key]*tmp
            num-=key*tmp
        return ans
```

### 13.罗马数字转整数
    链接：https://leetcode-cn.com/problems/roman-to-integer/

    罗马数字包含以下七种字符: I， V， X， L，C，D 和 M。

    字符          数值
    I             1
    V             5
    X             10
    L             50
    C             100
    D             500
    M             1000
    例如， 罗马数字 2 写做 II ，即为两个并列的 1。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。

    通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：

    I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
    X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
    C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。
    给定一个罗马数字，将其转换成整数。输入确保在 1 到 3999 的范围内。

    示例 1:

    输入: "III"
    输出: 3
    示例 2:

    输入: "IV"
    输出: 4
    示例 3:

    输入: "IX"
    输出: 9
    示例 4:

    输入: "LVIII"
    输出: 58
    解释: L = 50, V= 5, III = 3.
    示例 5:

    输入: "MCMXCIV"
    输出: 1994
    解释: M = 1000, CM = 900, XC = 90, IV = 4.

题解一|hash：

    首先建立一个HashMap来映射符号和值，然后对字符串从左到右来，如果当前字符代表的值小于其右边，就减去该值；否则就加上该值，以此类推到最右边的数
```
class Solution:
    def romanToInt(self, s: str) -> int:
        arr={'I':1,'V':5,'X':10,'L':50,'C':100,'D':500,'M':1000}
        ans=0
        for i in range(len(s)):
            if i<len(s)-1 and arr[s[i]]<arr[s[i+1]]:
                ans-=arr[s[i]]
            else:
                ans+=arr[s[i]]
        return ans
```

题解二|hash：

    构建一个字典记录所有罗马数字子串，注意长度为2的子串记录的值是（实际值 - 子串内左边罗马数字代表的数值）

    然后，遍历整个 s 的时候判断当前位置和前一个位置的两个字符组成的字符串是否在字典内，如果在就记录值，不在就说明当前位置不存在小数字在前面的情况，直接记录当前位置字符对应值

    举个例子，遍历经过 IV 的时候先记录 I 的对应值 1，再往前移动一步记录 IV 的值 3，加起来正好是 IV 的真实值 4。max 函数在这里是为了防止遍历第一个字符的时候出现 [-1:0] 的情况，因为s[-1:0]为空。

```
class Solution:
    def romanToInt(self, s: str) -> int:
        arr={'I':1,'IV':3,'V':5,'IX':8,'X':10,'XL':30,'L':50,'XC':80,'C':100,'CD':300,'D':500,'CM':800,'M':1000}
        return sum(arr.get(s[max(i-1,0):i+1],arr[v]) for i,v in enumerate(s))
```

```
class Solution:
    def romanToInt(self, s: str) -> int:
        arr={'I':1,'IV':3,'V':5,'IX':8,'X':10,'XL':30,'L':50,'XC':80,'C':100,'CD':300,'D':500,'CM':800,'M':1000}
        ans=0
        for i,v in enumerate(s):
            ans+=arr.get(s[i-1:i+1],arr[v])
        return ans
```


### 14.最长公共前缀
    链接：https://leetcode-cn.com/problems/longest-common-prefix/

    编写一个函数来查找字符串数组中的最长公共前缀。

    如果不存在公共前缀，返回空字符串 ""。

    示例 1:

    输入: ["flower","flow","flight"]
    输出: "fl"
    示例 2:

    输入: ["dog","racecar","car"]
    输出: ""
    解释: 输入不存在公共前缀。
    说明:

    所有输入只包含小写字母 a-z 。

https://blog.csdn.net/lz_901/article/details/89021210

题解一|水平扫描：

    1、先找出 str1 和 str2（注：str1代表第一个字符串，str2代表第二个） 的公共字符串 s1。

    2、然后再找出 s1 和 str3 的公共前缀 s2。

    3、然后再找出 s2 和 str4 的公共前缀 s3。

    4、一直这样遍历重复，用一个变量来保存两个两个字符串之间的公共前缀。

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/lcp1.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/lcp1.png)

```
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        if not strs:
            return ''
        prefix=strs[0]
        for i in range(1,len(strs)):
            while strs[i].find(prefix) != 0: # 此题是寻找最长公共前缀，所以需要找到开始索引是0.
                prefix=prefix[0:len(prefix)-1]
                if prefix=='':
                    return ''
        return prefix
```

find()语法：

    str.find(str, beg=0, end=len(string))

    参数：
        str -- 指定检索的字符串
        beg -- 开始索引，默认为0。
        end -- 结束索引，默认为字符串的长度。

    返回值：
        如果包含子字符串返回开始的索引值，否则返回-1。

题解二|水平扫描优化：

    想象数组的末尾有一个非常短的字符串，使用上述方法仍旧会进行S次比较。
    优化这类情况的方法是水平扫描，从前往后枚举字符串的每一列，先比较每个字符串相同列上的字符（即不同字符串相同下标的字符），然后再进行下一列的比较。

    我们不横向一个一个字符串遍历，而是采用纵向的方式。例如对于这个["flower","flow","flight"]，我们把它看成一个二维字符数组。

    然后纵向遍历，一列一列遍历，只要发现某一列出现不同的字符，就遍历结束，例如上面这个例子中，第三列就出现不同了，所以遍历结束，把前两列返回。

```
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        if not strs:
            return ''
        for i in range(len(strs[0])):
            tmp=strs[0][i]
            for j in range(1,len(strs)):
                if i==len(strs[j]) or strs[j][i]!=tmp:
                    return strs[0][0:i]
        return strs[0]

```

题解三|分治：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/lcp2.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/lcp2.png)

复杂度：
![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/lcp3.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/lcp3.png)
```
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        def commonPrefix(l, r):
            '''
            治：从首位开始对比
            :param l:
            :param r:
            :return:
            '''
            minlen = min(len(l), len(r))
            for i in range(minlen):
                if l[i] != r[i]:
                    return l[0:i]
            return l[0:minlen]

        def lcp(strs, l, r):
            '''
            分：分为小问题
            :param strs:
            :param l:
            :param r:
            :return:
            '''

            # 递归弹出
            if l == r:
                return strs[l]
            # 继续分
            else:
                mid = (l + r) // 2
                left = lcp(strs, l, mid)
                right = lcp(strs, mid + 1, r)
                return commonPrefix(left, right)

        if not strs:
            return ''
        return lcp(strs, 0, len(strs) - 1)
```

题解四|zip：
```
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        # print(strs)
        # print(*strs)
        res=''
        for i in zip(*strs):
            # print(i)
            if len(set(i))==1:
                res+=i[0]
            else:
                break
        return res

输出：
['flower', 'flow', 'flight']
flower flow flight
('f', 'f', 'f')
('l', 'l', 'l')
('o', 'o', 'i')
```

zip()语法：

    zip() 函数用于将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的列表(python2)/对象(python3)。

    如果各个迭代器的元素个数不一致，则返回列表长度与最短的对象相同，利用 * 号操作符，可以将元组解压为列表。

    zip 方法在 Python 2 和 Python 3 中的不同：在 Python 3.x 中为了减少内存，zip() 返回的是一个对象。如需展示列表，需手动 list() 转换。在 Python 2.x zip() 返回的是一个列表。

    zip([iterable, ...])

    参数说明：
        iterabl -- 一个或多个迭代器;
    返回值:
    返回元组列表/一个对象。

题解五|max和min：

max()和min()在python字符串中是可以比较的，按照ascii值排序，比如abb，aba，abac，最大为abb，最小为aba。

```
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        if not strs:
            return ''
        s1=min(strs)
        s2=max(strs)
        for i,v in enumerate(s1):
            if v != s2[i]:
                return s1[:i]
        return s1
```

### 28. 实现 strStr()
    链接：https://leetcode-cn.com/problems/implement-strstr/

    实现 strStr() 函数。

    给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。

    示例 1:

    输入: haystack = "hello", needle = "ll"
    输出: 2
    示例 2:

    输入: haystack = "aaaaa", needle = "bba"
    输出: -1
    说明:

    当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。

    对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与C语言的 strstr() 以及 Java的 indexOf() 定义相符。

题解一|子串逐一比较|线性时间复杂度：

思路：沿着字符换逐步移动滑动窗口，将窗口内的子串与 needle 字符串比较。

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/str1.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/str1.png)

时间复杂度：O((N - L)L)，其中 N 为 haystack 字符串的长度，L 为 needle 字符串的长度。内循环中比较字符串的复杂度为 L，总共需要比较 (N - L) 次。
空间复杂度：O(1)

```
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        n1,n2=len(haystack),len(needle)
        for i in range(n1-n2+1):
            if haystack[i:i+n2] == needle:
                return i
        return -1
```

题解二|双指针|线性时间复杂度：

题解一的缺陷是会将 haystack 所有长度为 L 的子串都与 needle 字符串比较，实际上是不需要这么做的。

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/str.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/str.png)

    首先，只有子串的第一个字符跟 needle 字符串第一个字符相同的时候才需要比较。

    其次，可以一个字符一个字符比较，一旦不匹配了就立刻终止。

    比较到最后一位时发现不匹配，这时候开始回溯。需要注意的是，i 指针是移动到 i = i - index + 1 的位置，而 不是 i = i - index 的位置。

    这时候再比较一次，就找到了完整匹配的子串，直接返回子串的开始位置 i - n2。

思路：

    移动 i 指针，直到 i 所指向位置的字符与 needle 字符串第一个字符相等。

    通过 i，j，index 计算匹配长度。

    如果完全匹配（即 index == n2），返回匹配子串的起始坐标（即 i - n2）。

    如果不完全匹配，回溯，使 i = i - index + 1， j = 0， index = 0。

时间复杂度：最坏时间复杂度为 O((n1 - n2)n2)，最优时间复杂度为 O(n2)。
空间复杂度：O(1)

```
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        n1,n2=len(haystack),len(needle)
        if not n2:
            return 0
        i=0
        while i<(n1-n2+1):
            while i<(n1-n2+1) and haystack[i] != needle[0]:
                i+=1
            j,index=0,0
            while i<n1 and j<n2 and haystack[i]==needle[j]:
                i+=1
                j+=1
                index+=1
            if index==n2:
                return i-n2
            i=i-index+1 # 如果使用i=i-index会超时。
        
        return -1
```

题解三|KMP：

参考：

https://leetcode-cn.com/problems/implement-strstr/solution/kmphua-48xiao-shi-kan-dong-liao-kmpxiang-rang-ni-z/

https://www.bilibili.com/video/BV1Px411z7Yo?from=search&seid=13225444196686531503

https://www.bilibili.com/video/BV1hW411a7ys/?spm_id_from=333.788.videocard.0

```
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        if not needle:
            return 0
        next=self.getNext(needle)
        i=j=0
        while i<len(haystack) and j<len(needle):
            if j==-1 or haystack[i]==needle[j]:
                i+=1
                j+=1
            else:
                j=next[j]
        if j==len(needle):
            return i-j
        else:
            return -1

    def getNext(self,needle):
        next=[0]*len(needle)
        next[0]=-1
        print(next)
        i=0
        j=-1
        while i<len(needle)-1:
            if j==-1 or needle[i]==needle[j]:
                i+=1
                j+=1
                next[i]=j
            else:
                j=next[j]
        return next
```

题解四：
```
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        i=haystack.find(needle)
        return i
```



### 38. 外观数列
    链接：https://leetcode-cn.com/problems/count-and-say/

    「外观数列」是一个整数序列，从数字 1 开始，序列中的每一项都是对前一项的描述。前五项如下：

    1.     1
    2.     11
    3.     21
    4.     1211
    5.     111221
    1 被读作  "one 1"  ("一个一") , 即 11。
    11 被读作 "two 1s" ("两个一"）, 即 21。
    21 被读作 "one 2",  "one 1" （"一个二" ,  "一个一") , 即 1211。

    给定一个正整数 n（1 ≤ n ≤ 30），输出外观数列的第 n 项。

    注意：整数序列中的每一项将表示为一个字符串。

    示例 1:

    输入: 1
    输出: "1"
    解释：这是一个基本样例。
    示例 2:

    输入: 4
    输出: "1211"
    解释：当 n = 3 时，序列是 "21"，其中我们有 "2" 和 "1" 两组，"2" 可以读作 "12"，也就是出现频次 = 1 而 值 = 2；类似 "1" 可以读作 "11"。所以答案是 "12" 和 "11" 组合在一起，也就是 "1211"。

https://zhuanlan.zhihu.com/p/106149461

题解一|递归:

```
class Solution:
    def countAndSay(self, n: int) -> str:
        if n==1:
            return '1'
        pre=self.countAndSay(n-1)
        res=''
        count=1
        n=len(pre)
        for i in range(1,n):
            if pre[i] != pre[i-1]:
                res+=str(count)+pre[i-1]
                count=1
            else:
                count+=1
        res+=str(count)+pre[n-1]
        return res

```

题解二|迭代:

```
class Solution:
    def countAndSay(self, n: int) -> str:
        res='1'
        while n-1>0:
            tmp=''
            count=0 # 计数
            pre=res[0] # 实际字符
            for i in range(len(res)):
                if pre==res[i]:
                    count+=1
                else:
                    tmp+=str(count)+pre
                    pre=res[i]
                    count=1
            tmp+=str(count)+pre
            res=tmp
            n-=1
        return res

```

```
class Solution:
    def countAndSay(self, n: int) -> str:
        res='1'
        for i in range(n-1,0,-1):
            tmp=''
            count=1 # count=1
            pre=res[0]
            for j in range(1,len(res)): # 从1开始
                if pre==res[j]:
                    count+=1
                else:
                    tmp+=str(count)+pre
                    pre=res[j]
                    count=1
            tmp+=str(count)+pre
            res=tmp
        return res
```


### 58. 最后一个单词的长度
    链接：https://leetcode-cn.com/problems/length-of-last-word/

    给定一个仅包含大小写字母和空格 ' ' 的字符串 s，返回其最后一个单词的长度。如果字符串从左向右滚动显示，那么最后一个单词就是最后出现的单词。

    如果不存在最后一个单词，请返回 0 。

    说明：一个单词是指仅由字母组成、不包含任何空格字符的 最大子字符串。

    示例:

    输入: "Hello World"
    输出: 5

题解一|内置函数:
```
class Solution:
    def lengthOfLastWord(self, s: str) -> int:
        return len(s.rstrip().split(' ')[-1])
```

题解二：
```
class Solution:
    def lengthOfLastWord(self, s: str) -> int:
        end=len(s)-1
        while end>=0 and s[end]==' ':
            end-=1
        if end < 0: return 0
        start=end
        while start >=0 and s[start] !=' ':
            start-=1
        return end-start
```

### 67. 二进制求和
    链接：https://leetcode-cn.com/problems/add-binary/

    给你两个二进制字符串，返回它们的和（用二进制表示）。

    输入为 非空 字符串且只包含数字 1 和 0。

    示例 1:

    输入: a = "11", b = "1"
    输出: "100"
    示例 2:

    输入: a = "1010", b = "1011"
    输出: "10101"
     

    提示：

    每个字符串仅由字符 '0' 或 '1' 组成。
    1 <= a.length, b.length <= 10^4
    字符串如果不是 "0" ，就都不含前导零。

题解一|内置函数:

时间复杂度为 O(N+M)

```
class Solution:
    def addBinary(self, a: str, b: str) -> str:
        return bin(int(a,2)+int(b,2))[2:]
```

题解二|按位运算：

时间复杂度：O(max(N,M))，其中 N 和 M 是输入字符串 a 和 b 的长度。

空间复杂度：O(max(N,M))，存储求和结果。

```
class Solution:
    def addBinary(self, a: str, b: str) -> str:
        n=max(len(a),len(b))
        a,b=a.zfill(n),b.zfill(n)
        carry=0
        res=[]
        for i in range(n-1,-1,-1):
            if a[i]=='1':
                carry+=1
            if b[i]=='1':
                carry+=1
            if carry%2==1:
                res.append('1')
            else:
                res.append('0')

            carry//=2

        if carry==1:
            res.append('1')
        res.reverse()
        return ''.join(res)
```

zfill()方法语法：
    str.zfill(width)
    参数:
        width -- 指定字符串的长度。原字符串右对齐，前面填充0。
    返回值:
        返回指定长度的字符串。

题解三|位运算：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/bit.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/bit.png)

算法:

    把 a 和 b 转换成整型数字 x 和 y，x 保存结果，y 保存进位。

    当进位不为 0：y != 0：

        计算当前 x 和 y 的无进位相加结果：answer = x^y。

        计算当前 x 和 y 的进位：carry = (x & y) << 1。

        完成本次循环，更新 x = answer，y = carry。

    返回 xx 的二进制形式。

时间复杂度：O(N+M)，其中 N 和 M 是输入字符串 a 和 b 的长度。

空间复杂度：O(max(N,M))，存储计算结果。

```
class Solution:
    def addBinary(self, a: str, b: str) -> str:
        x,y=int(a,2),int(b,2)
        while y:
            res=x^y
            carry=(x & y) << 1
            x,y=res,carry
        return bin(x)[2:]

```

```
class Solution:
    def addBinary(self, a, b) -> str:
        x, y = int(a, 2), int(b, 2)
        while y:
            x, y = x ^ y, (x & y) << 1
        return bin(x)[2:]

```

### 125.验证回文串
    链接：https://leetcode-cn.com/problems/valid-palindrome/

    给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

    说明：本题中，我们将空字符串定义为有效的回文串。

    示例 1:

    输入: "A man, a plan, a canal: Panama"
    输出: true
    示例 2:

    输入: "race a car"
    输出: false

题解一|双指针：
```
class Solution:
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

isalnum():检测字符串是否由字母和数字组成。

题解二|正则：
```
class Solution:
    def isPalindrome(self, s: str) -> bool:
        import re
        p=''.join(re.findall(r'[a-zA-Z0-9+]',s))
        p=p.lower()
        return True if p==p[::-1] else False
```

题解三|内置函数：
```
class Solution:
    def isPalindrome(self, s: str) -> bool:
        s = [*filter(str.isalnum, s.lower())]
        return s == s[::-1]
```

### 168.Excel表列名称
    链接：https://leetcode-cn.com/problems/excel-sheet-column-title/

    给定一个正整数，返回它在 Excel 表中相对应的列名称。

    例如，

        1 -> A
        2 -> B
        3 -> C
        ...
        26 -> Z
        27 -> AA
        28 -> AB 
        ...

    示例 1:

    输入: 1
    输出: "A"
    示例 2:

    输入: 28
    输出: "AB"
    示例 3:

    输入: 701
    输出: "ZY"


65--'A'
97--'a'

chr(65)  输出'A'
ord('A') 输出65

divmod() 函数把除数和余数运算结果结合起来，返回一个包含商和余数的元组(a // b, a % b)。

除留余数法：
1、将486由10进制转换为10进制

    486 / 10^0 % 10 = 6
    486 / 10^1 % 10 = 8
    486 / 10^2 % 10 = 4

    10 的 3 次幂大于 486 循环结束，可拆解出 10 进制数的个位、十位、百位…，再反向罗列得到 486

2、将486由10进制转换为2进制

    486 / 2^0 % 10 = 0
    486 / 2^1 % 10 = 1
    486 / 2^2 % 10 = 1
    486 / 2^3 % 10 = 0
    486 / 2^4 % 10 = 0
    486 / 2^5 % 10 = 1
    486 / 2^6 % 10 = 1
    486 / 2^7 % 10 = 1
    486 / 2^8 % 10 = 1

    2 的 9 次幂大于 486 循环结束，可拆解出 2 进制数 逻辑上的 个位、十位、百位…，再反向罗列得到 111100110

3、486 由 10 进制转换为 26 进制
    
    486 / 26^0 % 10 = 6
    486 / 26^1 % 10 = 1

10 进制包括数字：0~9
2 进制包括：0、1
26 进制应包括：0~25

题解一|转26进制：

```
class Solution:
    def convertToTitle(self, n: int) -> str:
        res=''
        while n:
            n,y=divmod(n,26)
            if y==0:
                n-=1
                y=26
            res=chr(y+64)+res
        return res
```

```
class Solution:
    def convertToTitle(self, n: int) -> str:
        res=''
        while n:
            n-=1 # 26字母中没有任何一个字母是表示0, 所以我们可以从 商 借一个给余数。
            n,y=divmod(n,26)
            res=chr(y+65)+res
        return res
```

题解二|递归：
```
class Solution:
    def convertToTitle(self, n: int) -> str:
        if n==0:
            return ''
        else:
            return self.convertToTitle((n-1)//26) + chr((n-1) % 26 + 65)
```



### 242. 有效的字母异位词
    链接：https://leetcode-cn.com/problems/valid-anagram/

    给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的字母异位词。

    示例 1:

    输入: s = "anagram", t = "nagaram"
    输出: true
    示例 2:

    输入: s = "rat", t = "car"
    输出: false
    说明:
    你可以假设字符串只包含小写字母。

    进阶:
    如果输入字符串包含 unicode 字符怎么办？你能否调整你的解法来应对这种情况？

题解一|排序：
```
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False
        if sorted(s) != sorted(t):
            return False
        return True
```

题解二|hash：
```
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False
        hash={}
        for i in range(len(s)):
            hash[s[i]]=hash.get(s[i],0)+1
            hash[t[i]]=hash.get(t[i],0)-1
        for k,v in hash.items():
            if v != 0:
                return False
        return True
```

题解三|hash:
```
class Solution(object):
    def isAnagram(self, s, t):
        if len(s) != len(t):
            return False
        dicts = collections.defaultdict(int)
        for i in range(len(s)):
            dicts[s[i]] = dicts[s[i]] + 1
            dicts[t[i]] = dicts[t[i]] - 1
        for val in dicts.values():
            if val != 0:
                return False
        return True
```

题解四|内置函数：
```
import collections
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        return collections.Counter(s) == collections.Counter(t)
```

### 344.反转字符串
    链接：https://leetcode-cn.com/problems/reverse-string/

    编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 char[] 的形式给出。

    不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题。

    你可以假设数组中的所有字符都是 ASCII 码表中的可打印字符。

    示例 1：

    输入：["h","e","l","l","o"]
    输出：["o","l","l","e","h"]
    示例 2：

    输入：["H","a","n","n","a","h"]
    输出：["h","a","n","n","a","H"]

字符串逆序：

方法一：利用字符串的切片来实现逆序

    def reverse1(str):
        return str[::-1]

方法二：将字符串转换为列表后再使用reverse()
    
    def reverse2(str):
        str_list=list(str)
        str_list.reverse()  # 列表翻转
        return ''.join(str_list) # 将列表转换为字符串

    def reverse2(str):
        str_list=[]
        for each in str:
            str_list.append(each)
        str_list.reverse()
        return ''.join(str_list)
方法三：新建列表，从后往前添加元素

    def reverse3(str):
        str_list=[]
        for i in range(len(str)-1,-1,-1):
            str_list.append(str[i])
        return ''.join(str_list)    
方法四：递归

    def reverse4(str):
        if len(str)<=1:
            return str
        return str[-1]+reverse4(str[:-1])
方法五：借助collections模块extendleft()

    import collections

    def reverse5(str):
        deque1 = collections.deque(str)
        deque2=collections.deque()
        for char in deque1:
            deque2.extendleft(char)
        return ''.join(deque2)
方法六：swap交换操作，以中间为基准，交换对称位置的字符

    def reverse5(str):
        str_list = list(str)
        if len(str_list) == 0 or len(str_list) == 1:
            return str
        i = 0
        length = len(str_list)
        while i < length / 2:
            str_list[i], str_list[length - i - 1] = str_list[length - i - 1], str_list[i]
            i += 1
        return ''.join(str_list)

题解一|内置函数：
```
class Solution:
    def reverseString(self, s):
        s.reverse()
```

题解二|递归：

时间复杂度：O(N)。执行了 N/2 次的交换。
空间复杂度：O(N)，递归过程中使用的堆栈空间。

```
class Solution:
    def reverseString(self, s: List[str]) -> None:
        """
        Do not return anything, modify s in-place instead.
        """
        def helper(left,right):
            if left<right:
                s[left],s[right]=s[right],s[left]
                helper(left+1,right-1)
        helper(0,len(s)-1)
```

题解三|双指针:

时间复杂度：O(N)。执行了 N/2 次的交换。
空间复杂度：O(1)，只使用了常数级空间。
```
class Solution:
    def reverseString(self, s: List[str]) -> None:
        """
        Do not return anything, modify s in-place instead.
        """
        left,right=0,len(s)-1
        while left<right:
            s[left],s[right]=s[right],s[left]
            left+=1
            right-=1
        return s
```

### 345. 反转字符串中的元音字母
    链接：https://leetcode-cn.com/problems/reverse-vowels-of-a-string/

    编写一个函数，以字符串作为输入，反转该字符串中的元音字母。

    示例 1:

    输入: "hello"
    输出: "holle"
    示例 2:

    输入: "leetcode"
    输出: "leotcede"

题解一|栈：
```
class Solution:
    def reverseVowels(self, s: str) -> str:
        stack=['a','e','i','o','u','A','E','I','O','U']
        res=[]
        tmp=[i for i in s if i in stack]
        for i in s:
            if i not in stack:
                res.append(i)
            else:
                res.append(tmp.pop())
        return ''.join(res)
```

题解二|双指针：
```
class Solution:
    def reverseVowels(self, s: str) -> str:
        if not s or len(s)==1:
            return s
        stack=['a','e','i','o','u','A','E','I','O','U']
        s=list(s)
        left,right=0,len(s)-1
        while left<right:
            if s[left] not in stack:
                left+=1
            if s[right] not in stack:
                right-=1
            if s[left] in stack and s[right] in stack:
                s[left],s[right]=s[right],s[left]
                left+=1
                right-=1
        return ''.join(s)
```

### 383. 赎金信
    链接：https://leetcode-cn.com/problems/ransom-note/

    给定一个赎金信 (ransom) 字符串和一个杂志(magazine)字符串，判断第一个字符串 ransom 能不能由第二个字符串 magazines 里面的字符构成。如果可以构成，返回 true ；否则返回 false。

    (题目说明：为了不暴露赎金信字迹，要从杂志上搜索各个需要的字母，组成单词来表达意思。杂志字符串中的每个字符只能在赎金信字符串中使用一次。) 

    注意：

    你可以假设两个字符串均只含有小写字母。

    canConstruct("a", "b") -> false
    canConstruct("aa", "ab") -> false
    canConstruct("aa", "aab") -> true

题解一|Counter：
```
class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        from collections import Counter
        r=Counter(ransomNote)
        m=Counter(magazine)
        if not r-m:
            return True
        else:
            return False
```

题解二|双指针：

时间复杂度为O(nlog(n)+mlog(m))
空间复杂度为O(m + n)

```
class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        ransomNoteList=list(ransomNote)
        magazineList=list(magazine)
        ransomNoteList.sort()
        magazineList.sort()
        i,j=0,0
        while i<len(ransomNoteList) and j<len(magazineList):
            if ransomNoteList[i]>magazineList[j]:
                j+=1
            elif ransomNoteList[i] < magazineList[j]:
                return False
            else:
                i+=1
                j+=1
        return i==len(ransomNoteList)
```

题解三|hash：

时间复杂度: O(N) - N（遍历magazine） + 1（字典检索为O(1)）
空间复杂度: O(N)

```
class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        hash={}
        for i in magazine:
            if i in hash:
                hash[i]+=1
            else:
                hash[i]=1
        for i in ransomNote:
            if i in hash and hash[i]>0:
                hash[i]-=1
            else:
                return False
        return True
```

题解四|暴力：

时间复杂度: O(N^2) - N（遍历ransomNote） * N（replace最糟糕的情况是遍历整个magazine）
空间复杂度: O(1)

```
class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        for i in ransomNote:
            if i in magazine:
                magazine=magazine.replace(i,'',1) # 可选字符串, 1代表替换不超过 max 次
            else:
                return False
        return True
```

### 387. 字符串中的第一个唯一字符
    链接：https://leetcode-cn.com/problems/first-unique-character-in-a-string/

    给定一个字符串，找到它的第一个不重复的字符，并返回它的索引。如果不存在，则返回 -1。

    案例:

    s = "leetcode"
    返回 0.

    s = "loveleetcode",
    返回 2.
     

    注意事项：您可以假定该字符串只包含小写字母。

题解一|切片：
```
class Solution:
    def firstUniqChar(self, s: str) -> int:
        if len(s)==1:
            return 0
        for k in range(len(s)):
            if (s[k] not in s[k+1:]) and (s[k] not in s[:k]):
                return k
        return -1
```

题解二|内置函数：

时间复杂度： O(N)，只遍历了两遍字符串，同时散列表中查找操作是常数时间复杂度的。
空间复杂度： O(N)，用到了散列表来存储字符串中每个元素出现的次数。

```
class Solution:
    def firstUniqChar(self, s: str) -> int:
        from collections import Counter
        count=Counter(s)
        for k,v in enumerate(s):
            if count[v]==1:
                return k
        return -1
```

```
import collections
class Solution:
    def firstUniqChar(self, s: str) -> int:
        for i,j in collections.Counter(s).items():
            if j==1:return s.index(i)
        return -1
```

题解三|find:

1.证明字母只出现了一次
如果一个字符串中的字符在字符串中从左边搜索和从右边搜索得到的index一样，那就证明只有一个了
2.循环每次是从第一个开始的，保证了执行的顺序


```
class Solution(object):
    def firstUniqChar(self, s):
        for i in s:
            if s.find(i)== s.rfind(i):
                return s.find(i)
        return -1
```

### 415. 字符串相加
    链接：https://leetcode-cn.com/problems/add-strings/

    给定两个字符串形式的非负整数 num1 和num2 ，计算它们的和。

    注意：

    num1 和num2 的长度都小于 5100.
    num1 和num2 都只包含数字 0-9.
    num1 和num2 都不包含任何前导零。
    你不能使用任何內建 BigInteger 库，也不能直接将输入的字符串转换为整数形式。


题解一：

zfill() 方法返回指定长度的字符串，原字符串右对齐，前面填充0。

```
class Solution:
    def addStrings(self, num1: str, num2: str) -> str:
        n=max(len(num1),len(num2))
        n1=num1.zfill(n)
        n2=num2.zfill(n)
        # print(n1,n2)
        carry=0
        res=[]
        for i in range(n-1,-1,-1):
            tmp=int(n1[i])+int(n2[i])
            carry+=tmp
            res.append(carry%10)
            carry//=10
        if carry==1:
            res.append(1)
        res.reverse() # res里面的数字是int
        return ''.join([str(i) for i in res])
```

题解二|双指针:

时间复杂度:O(max(M,N))：其中 M，N 为 2 数字长度，按位遍历一遍数字（以较长的数字为准）；
空间复杂度:O(1),指针与变量使用常数大小空间。

```
class Solution:
    def addStrings(self, num1: str, num2: str) -> str:
        res = ""
        i, j, carry = len(num1) - 1, len(num2) - 1, 0
        while i >= 0 or j >= 0:
            n1 = int(num1[i]) if i >= 0 else 0
            n2 = int(num2[j]) if j >= 0 else 0
            tmp = n1 + n2 + carry
            carry = tmp // 10
            res = str(tmp % 10) + res
            i, j = i - 1, j - 1
        return "1" + res if carry else res
```

### 434. 字符串中的单词数
    链接：https://leetcode-cn.com/problems/number-of-segments-in-a-string/

    统计字符串中的单词个数，这里的单词指的是连续的不是空格的字符。

    请注意，你可以假定字符串里不包括任何不可打印的字符。

    示例:

    输入: "Hello, my name is John"
    输出: 5
    解释: 这里的单词是指连续的不是空格的字符，所以 "Hello," 算作 1 个单词。

题解一|split:

时间复杂度 : O(n)。
这里用到的内置函数（无论是 Java 还是 Python）的时间复杂度或为 O(n)，或为 O(1) ，故整个算法可以在线性复杂度内完成。

空间复杂度 : O(n)。
split 函数 (不管哪种语言) 返回长度为 O(n) 的数组/列表，故算法使用线性的额外空间。

```
class Solution:
    def countSegments(self, s: str) -> int:
        if not s:
            return 0
        return len(s.split(' ')) # 这段代码是错误的，并不能解决连续多个空格的case
```

```
class Solution:
    def countSegments(self, s: str) -> int:
        return len(s.split())
```

区别：

    split()的时候，多个空格当成一个空格；
    split(' ')的时候，多个空格也要分割，会分割出来空。

题解二：

时间复杂度 : O(n)，对每个下标进行常数时间的检测。
空间复杂度 : O(1)，只使用了额外的几个整数，因此使用的空间为常数。

```
class Solution:
    def countSegments(self, s: str) -> int:
        count=0
        for i in range(len(s)):
            if s[i] != ' ' and (s[i-1] ==' ' or i==0 ):
                # print(s[i])
                count+=1
        return count 
```

### 520. 检测大写字母
    链接：https://leetcode-cn.com/problems/detect-capital/

    给定一个单词，你需要判断单词的大写使用是否正确。

    我们定义，在以下情况时，单词的大写用法是正确的：

    全部字母都是大写，比如"USA"。
    单词中所有字母都不是大写，比如"leetcode"。
    如果单词不只含有一个字母，只有首字母大写， 比如 "Google"。
    否则，我们定义这个单词没有正确使用大写字母。

    示例 1:

    输入: "USA"
    输出: True
    示例 2:

    输入: "FlaG"
    输出: False
    注意: 输入是由大写和小写拉丁字母组成的非空单词。

题解一|内置函数:
```
class Solution:
    def detectCapitalUse(self, word: str) -> bool:
        if word.isupper() or word.islower() or word.istitle():
            return True
        return False

class Solution:
    def detectCapitalUse(self, word: str) -> bool:
        return word.isupper() or word.islower() or word.istitle()
        
```
题解二：
```
class Solution:
    def detectCapitalUse(self, word: str) -> bool:
        c1=0
        c2=0
        for i in word:
            if (i>='a'):
                c1+=1
            else:
                c2+=1
        if (c1==len(word)) | (c2==len(word)):
            return True
                              
        return ((c2==1) & (word[0] < 'a' ))
```