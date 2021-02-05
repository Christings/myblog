---
title: LeetCode-2-栈
date: 2020-03-28 19:55:16
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
## 二、栈

### 20. 有效的括号
    链接：https://leetcode-cn.com/problems/valid-parentheses/

    给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

    有效字符串需满足：

    左括号必须用相同类型的右括号闭合。
    左括号必须以正确的顺序闭合。
    注意空字符串可被认为是有效字符串。

    示例 1:

    输入: "()"
    输出: true
    示例 2:

    输入: "()[]{}"
    输出: true
    示例 3:

    输入: "(]"
    输出: false
    示例 4:

    输入: "([)]"
    输出: false
    示例 5:

    输入: "{[]}"
    输出: true

题解一：

时间复杂度：O(N)。遍历了一遍字符串。
空间复杂度：O(N)。最坏情况下，假如输入是 (((((((，栈的大小将是输入字符串的长度。

```
class Solution:
    def isValid(self, s: str) -> bool:
        if len(s) %2 == 1:
            return False

        dict = {')':'(',']':'[','}':'{'}
        stack=[]
        for i in s:
            if stack and i in dict:
                if stack[-1] == dict[i]:
                    stack.pop()
                else:
                    return False
            else:
                stack.append(i)
        return not stack
```

```
class Solution:
    def isValid(self, s: str) -> bool:
        dicts={'(':1,')':-1,'{':2,'}':-2,'[':3,']':-3}
        list=[]
        for i in s:
            if len(list)==0:
                list.append(i)
            elif dicts[i]+dicts[list[-1]]==0:
                del list[-1]
            else:
                list.append(i)
        return len(list)==0
```

### 42. 接雨水

    链接：https://leetcode-cn.com/problems/trapping-rain-water/

    给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/youdao/water2.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/youdao/water2.png)

    上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 感谢 Marcos 贡献此图。

    示例:

    输入: [0,1,0,2,1,0,1,3,2,1,2,1]
    输出: 6

思路： 求出每个柱子上面能够存多少水，然后将每根柱子的存水量相加便能得到总的存水量，为求出每根柱子上能够存多少水，就要求出每根柱子左边最高的和右边最高柱子，然后用两者的最小值减去当前柱子的高度。 例如图中从左到右第三根柱子的高度为0，它左边最高柱子的值为1，右边最高柱子的值为3，因此它的最大存水量为 Min（1，3）-0=1。

原文讲解：https://blog.csdn.net/u013309870/article/details/70978279

题解一|暴力：
利用上面的思路，从左到右遍历每根柱子，遍历的时候求出每根柱子左边最高和右边最高柱子的值，然后利用两者的最小值减去当前柱子的高度就行了。

时间复杂度O（n^2）,空间复杂度O（1）。

注意：如果当前柱子大于它左右最大值的任何一个是存不了水的。
```
class Solution:
    def trap(self, height: List[int]) -> int:
        waters=0
        length=len(height)
        
        if height is None or length<2:
            return 0
        
        left,right=0,0
        for i in range(length):
            left,right=0,0
            for j in range(i):
                left=max(left,height[j])
            for j in range(length-1,i,-1):
                right=max(right,height[j])
            
            tmp=min(left,right)
            if tmp>height[i]:
                waters+=tmp-height[i]
            else:
                waters+=0
                
        return waters
```
题解二|优化：

题解一时间复杂度为O(n^2)。原因是对于每个元素都要从左到右，和从右到最左遍历其两边最大值，假如使用两个数组 left[ ] , right[ ]来保存每个元素左边最大值，右边最大值的话，这样就不用每次都遍历了，因此时间复杂度可以减少到O(n)，空间复杂度为O(n)，典型的空间换时间算法。

    对于数组[ 5, 2 , 6 , 2 , 4 ]
    它的左数组：[5,5,6,6,6]
    它的右数组：[6,6,6,4,4]

算法的流程：

    从左到右遍历一次求出每个元素左边的最大值，保存在 left 数组中。 
    从右到左遍历一次求出每个元素右边的最大值，保存在right数。
    最后一次遍历求出每个元素（每根柱子）的存水量。
```
class Solution:
    def trap(self, height: List[int]) -> int:
        waters=0
        length=len(height)
        
        if height is None or length<2:
            return 0
        leftLargest,rightLargest=0,0
        left,right=[0]*length,[0]*length
        
        for i in range(length):
            leftLargest=max(leftLargest,height[i])
            left[i]=leftLargest
        
        for i in range(length-1,-1,-1):
            rightLargest=max(rightLargest,height[i])
            right[i]=rightLargest
        
        for i in range(length):
            tmp=min(left[i],right[i])
            if tmp>height[i]:
                waters+=tmp-height[i]
            else:
                waters+=0
                
        return waters
```
题解三|优化：

优化题解二，分析上面算法发现其实没有必要使用 left 数组，因为当从左到右遍历求存水量的过程中可以利用一个变量来保存当前元素左边的最大值。

```
class Solution:
    def trap(self, height: List[int]) -> int:
        waters=0
        length=len(height)
        
        if height is None or length<2:
            return 0
        leftLargest,rightLargest=0,0
        right=[0]*length
        
        for i in range(length-1,-1,-1):
            rightLargest=max(rightLargest,height[i])
            right[i]=rightLargest
        
        for i in range(length):
            leftLargest=max(leftLargest,height[i])
            tmp=min(leftLargest,right[i])
            if tmp>height[i]:
                waters+=tmp-height[i]
            else:
                waters+=0
                
        return waters
```

题解四|双指针：

![image](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/youdao/water1.png)

    上面左右两边的黄色块分别表示当前元素左边最大值和右边最大值。

    left ，right分别代表从左到右移动和从右到左移动的指针。

    如果当前元素的左边最大值比右边最大值小，则left指针向右移动，否则right指针向左移动。

    这种左右指针移动的目的是为了保证所求的左右最大值一定是当前元素的左右最大值。

```
class Solution:
    def trap(self, height: List[int]) -> int:
        waters=0
        length=len(height)
        
        if height is None or length<2:
            return 0
        leftLargest,rightLargest=0,0
        left,right=0,length-1
        
        while left<right:
            leftLargest=max(leftLargest,height[left])
            rightLargest=max(rightLargest,height[right])
            
            if leftLargest > rightLargest:
                waters+=rightLargest-height[right]
                right-=1
            else:
                waters+=leftLargest-height[left]
                left+=1
                
        return waters
```

```
def trap(height):
    if not height: return 0
    left = 0
    right = len(height) - 1
    res = 0
    # 记录左右边最大值
    left_max = height[left]
    right_max = height[right]
    while left < right:
        if height[left] < height[right]:
            if left_max > height[left]:
                res += left_max - height[left]
            else:
                left_max = height[left]
            left += 1
        else:
            if right_max > height[right]:
                res += right_max - height[right]
            else:
                right_max = height[right]
            right -= 1
    return res
```
### 58.最后一个单词的长度
    链接：https://leetcode-cn.com/problems/length-of-last-word/

    给定一个仅包含大小写字母和空格 ' ' 的字符串，返回其最后一个单词的长度。

    如果不存在最后一个单词，请返回 0 。

    说明：一个单词是指由字母组成，但不包含任何空格的字符串。

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
思路:
    从字符串末尾开始向前遍历，其中主要有两种情况
    第一种情况，以字符串"Hello World"为例，从后向前遍历直到遍历到头或者遇到空格为止，即为最后一个单词"World"的长度5
    第二种情况，以字符串"Hello World "为例，需要先将末尾的空格过滤掉，再进行第一种情况的操作，即认为最后一个单词为"World"，长度为5
    所以完整过程为先从后过滤掉空格找到单词尾部，再从尾部向前遍历，找到单词头部，最后两者相减，即为单词的长度
    时间复杂度：O(n)，n为结尾空格和结尾单词总体长度

```
class Solution:
    def lengthOfLastWord(self, s: str) -> int:
        # return len(s.rstrip().split(' ')[-1])
        end=len(s)-1
        while end>=0 and s[end]==' ':
            end-=1
        if end < 0: return 0
        start=end
        while start >=0 and s[start] !=' ':
            start-=1
        return end-start
```

### 84. 柱状图中最大的矩形
    链接：https://leetcode-cn.com/problems/largest-rectangle-in-histogram/

    给定 n 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。

    求在该柱状图中，能够勾勒出来的矩形的最大面积。

    以上是柱状图的示例，其中每个柱子的宽度为 1，给定的高度为 [2,1,5,6,2,3]。

    图中阴影部分为所能勾勒出的最大矩形面积，其面积为 10 个单位。

    示例:

    输入: [2,1,5,6,2,3]
    输出: 10

题解一|暴力|超时：
```
class Solution:
    def largestRectangleArea(self, heights: List[int]) -> int:
        if not heights:
            return 0
        n=len(heights)
        area=0
        for i in range(n):
            tmp=heights[i]
            for j in range(i,n):
                tmp=min(tmp,heights[j])
                area=max(area,tmp*(j-i+1))
        return area
```

题解二|递增栈：
```
class Solution:
    def largestRectangleArea(self, heights: List[int]) -> int:
        if not heights:
            return 0
        n=len(heights)
        stack=[]
        area,i=0,0
        while i < n:
            if not stack or (heights[i] > heights[stack[-1]]):
                stack.append(i)
                i+=1
            else:
                tmp=stack.pop()
                area=max(area,heights[tmp]*((i-stack[-1]-1) if stack else i))
        while stack:
            tmp=stack.pop()
            area=max(area,heights[tmp]*((i-stack[-1]-1) if stack else i))
        return area
```

### 85. 最大矩形
    链接：https://leetcode-cn.com/problems/maximal-rectangle/

    给定一个仅包含 0 和 1 的二维二进制矩阵，找出只包含 1 的最大矩形，并返回其面积。

    示例:

    输入:
    [
      ["1","0","1","0","0"],
      ["1","0","1","1","1"],
      ["1","1","1","1","1"],
      ["1","0","0","1","0"]
    ]
    输出: 6

题解一|暴力：

时间复杂度：O（m²n）。
空间复杂度：O（mn）。

```
class Solution:
    def maximalRectangle(self, matrix: List[List[str]]) -> int:
        if not matrix:
            return 0
        m=len(matrix)
        n=len(matrix[0])
        dp=[[0] * n for i in range(m)]
        area=0
        for i in range(m):
            for j in range(n):
                if matrix[i][j]=='1':
                    if n==0:
                        dp[i][j]=1
                    else:
                        dp[i][j]=dp[i][j-1]+1
                else:
                    dp[i][j]=0   

                minWidth=dp[i][j]
                for k in range(i,-1,-1):
                    height=i-k+1
                    minWidth=min(minWidth,dp[k][j])
                    area=max(area,height*minWidth)
            
        return area
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



### 409.最长回文串
    链接：https://leetcode-cn.com/problems/longest-palindrome/

    给定一个包含大写字母和小写字母的字符串，找到通过这些字母构造成的最长的回文串。

    在构造过程中，请注意区分大小写。比如 "Aa" 不能当做一个回文字符串。

    注意:
    假设字符串的长度不会超过 1010。

    示例 1:

    输入:
    "abccccdd"

    输出:
    7

    解释:
    我们可以构造的最长的回文串是"dccaccd", 它的长度是 7。

题解一|hash：

时间复杂度：O(N)，其中 N 为字符串 s 的长度。我们需要遍历每个字符一次。

空间复杂度：O(S)，其中 S 为字符集大小。

```
class Solution:
    def longestPalindrome(self, s: str) -> int:
        if not s:
            return 0
        hash={}
        sum=0
        for each in s:
            if each not in hash:
                hash[each]=1
            else:
                hash[each]+=1
        for key,value in hash.items():
            sum+=value//2*2
            if sum%2==0 and value%2==1:
                sum+=1
        return sum
```

```
class Solution:
    def longestPalindrome(self, s):
        ans = 0
        count = collections.Counter(s)
        for v in count.values():
            ans += v // 2 * 2
            if ans % 2 == 0 and v % 2 == 1:
                ans += 1
        return ans
```

### 836.矩形重叠
    链接：https://leetcode-cn.com/problems/rectangle-overlap/

    矩形以列表 [x1, y1, x2, y2] 的形式表示，其中 (x1, y1) 为左下角的坐标，(x2, y2) 是右上角的坐标。

    如果相交的面积为正，则称两矩形重叠。需要明确的是，只在角或边接触的两个矩形不构成重叠。

    给出两个矩形，判断它们是否重叠并返回结果。

     

    示例 1：

    输入：rec1 = [0,0,2,2], rec2 = [1,1,3,3]
    输出：true
    示例 2：

    输入：rec1 = [0,0,1,1], rec2 = [1,0,2,1]
    输出：false
     

    提示：

    两个矩形 rec1 和 rec2 都以含有四个整数的列表的形式给出。
    矩形中的所有坐标都处于 -10^9 和 10^9 之间。
    x 轴默认指向右，y 轴默认指向上。
    你可以仅考虑矩形是正放的情况。

题解一|检查位置：

思路：如果两个矩形不重叠，那么一个矩形肯定在另一个矩形的四周（即左右上下）

    矩形 rec1 在矩形 rec2 的左侧；

    矩形 rec1 在矩形 rec2 的右侧；

    矩形 rec1 在矩形 rec2 的上方；

    矩形 rec1 在矩形 rec2 的下方。

    左侧：rec1[2] <= rec2[0]；

    右侧：rec1[0] >= rec2[2]；

    上方：rec1[1] >= rec2[3]；

    下方：rec1[3] <= rec2[1]。

时间复杂度：O(1)
空间复杂度：O(1)，不需要额外的空间。

```
class Solution:
    def isRectangleOverlap(self, rec1: List[int], rec2: List[int]) -> bool:
        if rec1[0]>=rec2[2] or rec1[1]>=rec2[3]:
            return False
        if rec2[0]>=rec1[2] or rec2[1]>=rec1[3]: # 注意是or，不是and。
            return False
        return True
```

```
class Solution:
    def isRectangleOverlap(self, rec1: List[int], rec2: List[int]) -> bool:
        if rec1[2]<=rec2[0] or rec1[3]<=rec2[1] or rec1[0]>=rec2[2] or rec1[1]>=rec2[3]:
            return False
        else:
            return True
```

题解二|检查区域：

思路：如果两个矩形重叠，那么它们重叠的区域一定也是一个矩形，那么这代表了两个矩形与 x 轴平行的边（水平边）投影到 x 轴上时会有交集，与 y 轴平行的边（竖直边）投影到 y 轴上时也会有交集。因此，我们可以将问题看作一维线段是否有交集的问题。

矩形 rec1 和 rec2 的水平边投影到 x 轴上的线段分别为 (rec1[0], rec1[2]) 和 (rec2[0], rec2[2])。根据数学知识我们可以知道，当 min(rec1[2], rec2[2]) > max(rec1[0], rec2[0]) 时，这两条线段有交集。对于矩形 rec1 和 rec2 的竖直边投影到 yy 轴上的线段，同理可以得到，当 min(rec1[3], rec2[3]) > max(rec1[1], rec2[1]) 时，这两条线段有交集。（画图）

时间复杂度：O(1)
空间复杂度：O(1)，不需要额外的空间。

```
class Solution:
    def isRectangleOverlap(self, rec1: List[int], rec2: List[int]) -> bool:
        def isIntersec(pLeft,pRight,qLeft,qRight):
            return min(pRight,qRight)>max(pLeft,qLeft)
        return isIntersec(rec1[0],rec1[2],rec2[0],rec2[2]) and isIntersec(rec1[1],rec1[3],rec2[1],rec2[3])
```

### 1160.拼写单词
    
    链接：https://leetcode-cn.com/problems/find-words-that-can-be-formed-by-characters/

    给你一份『词汇表』（字符串数组） words 和一张『字母表』（字符串） chars。

    假如你可以用 chars 中的『字母』（字符）拼写出 words 中的某个『单词』（字符串），那么我们就认为你掌握了这个单词。

    注意：
        每次拼写时，chars 中的每个字母都只能用一次。
        返回词汇表 words 中你掌握的所有单词的 长度之和。

    示例 1：

    输入：words = ["cat","bt","hat","tree"], chars = "atach"
    输出：6
    解释： 
    可以形成字符串 "cat" 和 "hat"，所以答案是 3 + 3 = 6。
    示例 2：

    输入：words = ["hello","world","leetcode"], chars = "welldonehoneyr"
    输出：10
    解释：
    可以形成字符串 "hello" 和 "world"，所以答案是 5 + 5 = 10。
 
    提示：
    1 <= words.length <= 1000
    1 <= words[i].length, chars.length <= 100
    所有字符串中都仅包含小写英文字母

题解一（hash）：

思路：对于一个单词 word，只要其中的每个字母的数量都不大于 chars 中对应的字母的数量，那么就可以用 chars 中的字母拼写出 word。所以我们只需要用一个哈希表存储 chars 中每个字母的数量，再用一个哈希表存储 word 中每个字母的数量，最后将这两个哈希表的键值对逐一进行比较即可。

时间复杂度：O(n)，其中 n 为所有字符串的长度和。我们需要遍历每个字符串，包括 chars 以及数组 words 中的每个单词。

空间复杂度：O(S)，其中 S 为字符集大小，在本题中 S 的值为 26（所有字符串仅包含小写字母）。程序运行过程中，最多同时存在两个哈希表，使用的空间均不超过字符集大小 S，因此空间复杂度为 O(S)。

```
class Solution:
    def countCharacters(self, words: List[str], chars: str) -> int:
        from collections import Counter
        hashChars={}
        hashWords={}
        hashChars=Counter(chars)
        res=0
        # print(hashChars)
        for word in words:
            hashWords=Counter(word)
            # print(hashWords)
            for key in hashWords:
                if hashChars[key] < hashWords[key]:
                    break 
            else:
                res+=len(word)
        return res
```

```
class Solution:
    def countCharacters(self, words: List[str], chars: str) -> int:
        res=0
        hash1={}
        for each in chars:
            if each not in hash1:
                hash1[each]=1
            else:
                hash1[each]+=1
        for word in words:
            hash2={}
            for w in word:
                if w not in hash2:
                    hash2[w]=1
                else:
                    hash2[w]+=1
            for key in hash2:
                if hash2.get(key,0) > hash1.get(key,0):
                    break
            else:
                res+=len(word)
        return res
```

for-else用法：
    
    如果for循环正常结束，else中语句执行；
    如果for循环是break退出的，则else中语句不执行；

```
for i in range(5):
    print(i)
else:
    print('hello')
    
for i in range(5):
    print(i)
    if i==2:
        break
else:
    print('hello')

输出：
0
1
2
3
4
hello
------------
0
1
2
```

题解二|hash：
    
    dict.get(key, default=None)
    key -- 字典中要查找的键。
    default -- 如果指定键的值不存在时，返回该默认值。

```
class Solution:
    def countCharacters(self, words: List[str], chars: str) -> int:
        res=''
        for word in words:
            hash={}
            for i in range(len(chars)):
                hash[chars[i]]=hash.get(chars[i],0)+1
            count=0
            for w in word:
                if hash.get(w,0)>=1:
                    hash[w]=hash.get(w,0)-1
                    count+=1
                else:
                    break
            if count == len(word):
                res+=word
        return len(res)
```

