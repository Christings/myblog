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


题解一|暴力破解：
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
                    # print(i,j)
                    # print(s[i:j+1])
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

题解二|滑动窗口|万能模版：
```
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        from collections import defaultdict
        lookup=defaultdict(int) # 使用int的话，默认值变为0
        left,right=0,0
        res=0
        count=0
        while right < len(s):
            if lookup[s[right]]>0:
                count+=1
            lookup[s[right]]+=1
            right+=1
            while count > 0:
                if lookup[s[left]]>1:
                    count-=1
                lookup[s[left]]-=1
                left+=1
            res=max(res,right-left)
        return res
```

题解二|滑动窗口：

    我们使用两个指针表示字符串中的某个子串的左右边界，其中左指针代表着上文中「枚举子串的起始位置」，而右指针即为上文中的 right；

    在每一步的操作中，我们会将左指针向右移动一格，表示我们开始枚举下一个字符作为起始位置，然后我们可以不断地向右移动右指针，但需要保证这两个指针对应的子串中没有重复的字符。在移动结束后，这个子串就对应着以左指针开始的，不包含重复字符的最长子串。我们记录下这个子串的长度；

    在枚举结束后，我们找到的最长的子串的长度即为答案。

时间复杂度：O(N)，其中 N 是字符串的长度。左指针和右指针分别会遍历整个字符串一次。
空间复杂度：O(∣Σ∣)

```
class Solution:
    def lengthOfLongestSubstring(self, s):
        """
        :type s: str
        :rtype: int
        """
        occ=set()
        n=len(s)
        right,ans=-1,0
        for i in range(n):
            if i!=0:
                occ.remove(s[i-1])
            while right+1<n and s[right+1] not in occ:
                occ.add(s[right+1])
                right+=1
            ans=max(ans,right-i+1)
        return ans
```

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
        n,length=len(haystack),len(needle)
        for i in range(n-length+1):
            if haystack[i:i+length]==needle:
                return i
        return -1
```

题解二|双指针:

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
        m,n=len(haystack),len(needle)
        if n==0:
            return 0
        p1=0
        while p1<m-n+1:
            while p1<m-n+1 and haystack[p1] != needle[0]:
                p1+=1
            curr=p2=0
            while p1<m and p2<n and haystack[p1]==needle[p2]:
                p1+=1
                p2+=1
                curr+=1
            if curr==n:
                return p1-n
            p1=p1-curr+1
        return -1
```

？？？题解三|KMP：
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
        # print(next)
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

### 30. 串联所有单词的子串
    链接：https://leetcode-cn.com/problems/substring-with-concatenation-of-all-words/

    给定一个字符串 s 和一些长度相同的单词 words。找出 s 中恰好可以由 words 中所有单词串联形成的子串的起始位置。

    注意子串要与 words 中的单词完全匹配，中间不能有其他字符，但不需要考虑 words 中单词串联的顺序。

    示例 1：

    输入：
      s = "barfoothefoobarman",
      words = ["foo","bar"]
    输出：[0,9]
    解释：
    从索引 0 和 9 开始的子串分别是 "barfoo" 和 "foobar" 。
    输出的顺序不重要, [9,0] 也是有效答案。
    示例 2：

    输入：
      s = "wordgoodgoodgoodbestword",
      words = ["word","good","best","word"]
    输出：[]

题解一|逐字母前进：

    时间复杂度：O(m*n)
```
class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        ans=[]
        if not s or not words:
            return ans

        word_hash={}
        for each in words:
            word_hash[each]=word_hash.get(each,0)+1

        s_len,w_len=len(s),len(words[0])
        n=w_len * len(words) # 符合要求的子串长度
        
        for i in range(s_len-n+1):
            res_hash=word_hash.copy()
            j=i+w_len
            while s[j-w_len:j] in res_hash and res_hash[s[j-w_len:j]]>0:
                res_hash[s[j-w_len:j]]-=1
                j+=w_len
            if sum(res_hash.values())==0:
                ans.append(i)
        return ans
```

```
class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        ans=[]
        if not s or not words:
            return ans

        word_hash={}
        for each in words:
            word_hash[each]=word_hash.get(each,0)+1

        s_len,w_len=len(s),len(words[0])
        n=w_len * len(words) # 符合要求的子串长度
        
        for i in range(s_len-n+1):
            tmp=s[i:i+n]
            res_hash={}
            for j in range(0,n,w_len):
                res_hash[tmp[j:j+w_len]]=res_hash.get(tmp[j:j+w_len],0)+1
            if word_hash==res_hash:
                ans.append(i)
        return ans
```

题解二|逐单词前进

```
class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        ans=[]
        if not words:
            return ans

        word_hash={}
        for each in words:
            word_hash[each]=word_hash.get(each,0)+1

        s_len,w_len=len(s),len(words[0])
        n=w_len * len(words) # 符合要求的子串长度
        
        for offset in range(w_len):
            tmp_hash={}
            for i in range(offset,offset+n-w_len,w_len):
                tmp_hash[s[i:i+w_len]]=tmp_hash.get(s[i:i+w_len],0)+1
            for begin in range(offset,s_len-n+1,w_len):
                tmp_hash[s[begin+n-w_len:begin+n]]=tmp_hash.get(s[begin+n-w_len:begin+n],0)+1
                if tmp_hash==word_hash:
                    ans.append(begin)
                tmp_hash[s[begin:begin+w_len]]-=1
                if tmp_hash[s[begin:begin+w_len]]==0:
                    del tmp_hash[s[begin:begin+w_len]]
        return ans

```


```
class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        from collections import Counter
        ans=[]
        if not s or not words:
            return ans

        word_hash=Counter(words)

        s_len,w_len=len(s),len(words[0])
        n=w_len * len(words) # 符合要求的子串长度
        
        for i in range(w_len):
            cnt=0
            left,right=i,i
            cur_hash=Counter()
            while right+w_len <= s_len:
                each=s[right:right+w_len]
                right+=w_len
                cur_hash[each]+=1
                cnt+=1
                print(cur_hash)
                while cur_hash[each] > word_hash[each]:
                    tmp=s[left:left+w_len]
                    left+=w_len
                    cur_hash[tmp]-=1
                    cnt-=1
                if cnt==len(words):
                    ans.append(left)
        return ans
```

优化：
```
class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        from collections import Counter
        ans=[]
        if not s or not words:
            return ans

        word_hash=Counter(words)

        s_len,w_len=len(s),len(words[0])
        n=w_len * len(words) # 符合要求的子串长度
        
        for i in range(w_len):
            cnt=0
            left,right=i,i
            cur_hash=Counter()
            while right+w_len <= s_len:
                each=s[right:right+w_len]
                right+=w_len
                if each not in words:
                    left=right
                    cur_hash.clear()
                    cnt=0
                else:
                    cur_hash[each]+=1
                    cnt+=1
                    while cur_hash[each] > word_hash[each]:
                        tmp=s[left:left+w_len]
                        left+=w_len
                        cur_hash[tmp]-=1
                        cnt-=1
                    if cnt==len(words):
                        ans.append(left)
        return ans
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

题解一|滑动窗口|万能模版：

```
class Solution:
    def minWindow(self, s: str, t: str) -> str:
        from collections import defaultdict
        lookup=defaultdict(int)
        for each in t:
            lookup[each]+=1
        left,right=0,0
        count=len(t)
        min_len=float('inf')
        res=''

        while right<len(s):
            if lookup[s[right]]>0:
                count-=1
            lookup[s[right]]-=1
            right+=1
            while count==0:
                if min_len>right-left:
                    min_len=right-left
                    res=s[left:right]
                if lookup[s[left]]==0:
                    count+=1
                lookup[s[left]]+=1
                left+=1
        return res

```

题解一|滑动窗口：

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

### 159. 至多包含两个不同字符的最长子串

    给定一个字符串 s ，找出 至多 包含两个不同字符的最长子串 t 。

    示例 1:

    输入: "eceba"
    输出: 3
    解释: t 是 "ece"，长度为3。
    1
    2
    3
    示例 2:

    输入: "ccaabbb"
    输出: 5
    解释: t 是 "aabbb"，长度为5。

题解一|滑动窗口|万能模版：

```
class Solution:
    def lengthOfLongestSubstringTwoDistinct(self, s: str) -> int:
        from collections import defaultdict
        lookup=defaultdict(int)
        left,right=0,0
        res=0
        count=0
        while right<len(s):
            if lookup[s[right]]==0:
                count+=1
            lookup[s[right]]+=1
            right+=1
            while count>2:
                if lookup[s[left]]==1:
                    count-=1
                lookup[s[left]]-=1
                left+=1
            res=max(res,right-left)
            return res
```

### 340. 至多包含 K 个不同字符的最长子串

    给定一个字符串 s ，找出 至多 包含 k 个不同字符的最长子串 T。

    示例1：

    输入: s = "eceba", k = 2
    输出: 3
    解释: 则 T 为 "ece"，所以长度为 3。

题解一|滑动窗口|万能模版：
```
class Solution:
    def lengthOfLongestSubstringKDistinct(self, s: str, k: int) -> int:
        from collections import defaultdict
        lookup=defaultdict(int)
        left,right=0,0
        res=0
        count=0
        while right < len(s):
            lookup[s[right]]==0:
                count+=1
            lookup[s[right]]+=1
            right+=1
            while count > k:
                if lookup[s[left]]==1:
                    count-=1
                lookup[s[left]]-=1
                left+=1
            res=max(res,right-left)
        return res
```
