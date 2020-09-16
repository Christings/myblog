---
title: LeetCode-11-滑动窗口
date: 2020-02-19 21:23:58
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
## 十一、滑动窗口

### 3.无重复字符的最长子串
    链接：https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/

    给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

    示例 1:

    输入: "abcabcbb"
    输出: 3 
    解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
    示例 2:

    输入: "bbbbb"
    输出: 1
    解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
    示例 3:

    输入: "pwwkew"
    输出: 3
    解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
         请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
题解一（暴力破解）：
时间复杂度O(n^3)
```
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        length=len(s)

        if s == '':
            return 0
        ans=1
        
        for i in range(length):
            for j in range(i+1,length):  
                if s[i] != s[j]:
                    print(i,j)
                    print(s[i:j+1])
                    if self.isUnique(s[i:j+1]):
                        ans=max(ans,len(s[i:j+1]))
        return ans

    def isUnique(self,s):
        for i in range(len(s)):
            if s[i] in s[i+1:]:
                return False
        return True
```
输出：
```
0 1
ha
0 3
haha
1 2
ah
2 3
ha
```
题解二（滑动窗口）：

什么是滑动窗口？

    其实就是一个队列,比如例题中的 abcabcbb，进入这个队列（窗口）为 abc 满足题目要求，当再进入 a，队列变成了 abca，这时候不满足要求。所以，我们要移动这个队列！

    如何移动？

    我们只要把队列的左边的元素移出就行了，直到满足题目要求！

    一直维持这样的队列，找出队列出现最长的长度时候，求出解！

时间复杂度：O(n)

```
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        if s is None:
            return 0
        maxLen=0
        curLen=0
        left=0
        lookup=set()
        for i in range(len(s)):
            curLen+=1
            while s[i] in lookup: # 注意此处是while，如果s[i]在lookup里，会一直循环
                print(left,i,s[left],lookup)
                lookup.remove(s[left]) # set是无序无排列的，注意是s[left]
                left+=1
                curLen-=1
            maxLen=max(maxLen,curLen)
            lookup.add(s[i])
        return maxLen
```
输入："abcabcbb"
输出：
```
0 3 a {'c', 'a', 'b'}
1 4 b {'c', 'a', 'b'}
2 5 c {'c', 'a', 'b'}
3 6 a {'c', 'a', 'b'}
4 6 b {'c', 'b'}
5 7 c {'c', 'b'}
6 7 b {'b'}


```

另一种写法：
```
from collection improt defaultdict

class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        lookup=defaultdict(int)
        start=0
        end=0
        maxLen=0
        counter=0
        while end<len(s):
            if lookup[s[end]]>0:
                counter+=1
            lookup[s[end]]+=1
            end+=1
            while counter>0:
                if lookup[s[start]]>1:
                    counter-=1
                lookup[s[start]]-=1
                start+=1
            maxLen=max(maxLen,end-start)
        return maxLen
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

题解一(子串逐一比较 - 线性时间复杂度)：

思路：沿着字符换逐步移动滑动窗口，将窗口内的子串与 needle 字符串比较。

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/str1.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/str1.png)

时间复杂度：O((N - L)L)，其中 N 为 haystack 字符串的长度，L 为 needle 字符串的长度。内循环中比较字符串的复杂度为 L，总共需要比较 (N - L) 次。
空间复杂度：O(1)

```
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        n,length=len(haystack),len(needle)
        for i in range(n-length+1):
            if haystack[i:i+length]==needle:
                return i
        return -1
```

？？题解二（双指针 - 线性时间复杂度）：

题解一的缺陷是会将 haystack 所有长度为 L 的子串都与 needle 字符串比较，实际上是不需要这么做的。

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/str.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/str.png)

    首先，只有子串的第一个字符跟 needle 字符串第一个字符相同的时候才需要比较。

    其次，可以一个字符一个字符比较，一旦不匹配了就立刻终止。

    比较到最后一位时发现不匹配，这时候开始回溯。需要注意的是，pn 指针是移动到 pn = pn - curr_len + 1 的位置，而 不是 pn = pn - curr_len 的位置。

    这时候再比较一次，就找到了完整匹配的子串，直接返回子串的开始位置 pn - L。

思路：

    移动 pn 指针，直到 pn 所指向位置的字符与 needle 字符串第一个字符相等。

    通过 pn，pL，curr_len 计算匹配长度。

    如果完全匹配（即 curr_len == L），返回匹配子串的起始坐标（即 pn - L）。

    如果不完全匹配，回溯。使 pn = pn - curr_len + 1， pL = 0， curr_len = 0。

时间复杂度：最坏时间复杂度为 O((N - L)L)，最优时间复杂度为 O(N)。
空间复杂度：O(1)

```
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        L, n = len(needle), len(haystack)
        if L == 0:
            return 0

        pn = 0
        while pn < n - L + 1:
            # find the position of the first needle character
            # in the haystack string
            while pn < n - L + 1 and haystack[pn] != needle[0]:
                pn += 1
            
            # compute the max match string
            curr_len = pL = 0
            while pL < L and pn < n and haystack[pn] == needle[pL]:
                pn += 1
                pL += 1
                curr_len += 1
            
            # if the whole needle string is found,
            # return its start position
            if curr_len == L:
                return pn - L
            
            # otherwise, backtrack
            pn = pn - curr_len + 1
            
        return -1
```

？？？题解三（KMP）：
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


### 76.最小覆盖子串
    链接：https://leetcode-cn.com/problems/minimum-window-substring/

    给你一个字符串 S、一个字符串 T，请在字符串 S 里面找出：包含 T 所有字母的最小子串。

    示例：

    输入: S = "ADOBECODEBANC", T = "ABC"
    输出: "BANC"
    说明：

    如果 S 中不存这样的子串，则返回空字符串 ""。
    如果 S 中存在这样的子串，我们保证它是唯一的答案。

题解一（滑动窗口）：

    保证窗口(队列)字符串含有t中字符的个数大于等于t里相应元素个数
```
class Solution:
    def minWindow(self, s: str, t: str) -> str:
        from collections import Counter
        t=Counter(t)
        lookup=Counter()
        start,end=0,0
        minLen=float('inf')
        res=''
        while end < len(s):
            lookup[s[end]]+=1
            end+=1
            while all(map(lambda x:lookup[x] >= t[x],t.keys())):
                # print(lookup)
                if end-start < minLen:
                    res=s[start:end]
                    minLen=end-start
                lookup[s[start]]-=1
                start+=1
        return res
```

    记录队列元素和t中元素的差值
```
class Solution:
    def minWindow(self, s: str, t: str) -> str:
        from collections import defaultdict
        lookup=defaultdict(int)
        for each in t:
            lookup[each]+=1
        start,end=0,0
        minLen=float('inf')
        counter=len(t)
        res=''
        while end < len(s):
            if lookup[s[end]] > 0:
                counter-=1
            lookup[s[end]]-=1
            end+=1
            while counter==0:
                if minLen> end-start:
                    minLen=end-start
                    res=s[start:end]
                if lookup[s[start]]==0:
                    counter+=1
                lookup[s[start]]+=1
                start+=1
        return res

```