---
title: LeetCode-6-贪心
date: 2020-01-31 12:09:27
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
## 六、贪心

贪心和动态规划一样，考验的是对问题分析的能力，贪心算法解题的关键在于如何找到每次的局部最优解，动态规划则是如何找到状态转移方程。

什么是贪心算法呢？贪心算法可以认为是动态规划算法的一个特例，相比动态规划，使用贪心算法需要满足更多的条件（贪心选择性质），但是效率比动态规划要高。

比如说一个算法问题使用暴力解法需要指数级时间，如果能使用动态规划消除重叠子问题，就可以降到多项式级别的时间，如果满足贪心选择性质，那么可以进一步降低时间复杂度，达到线性级别的。

什么是贪心选择性质呢，简单说就是：每一步都做出一个局部最优的选择，最终的结果就是全局最优。注意哦，这是一种特殊性质，其实只有一小部分问题拥有这个性质。

问题：Interval Scheduling（区间调度问题）。给你很多形如[start,end]的闭区间，请你设计一个算法，算出这些区间中最多有几个互不相交的区间。

举个例子，intvs=[[1,3],[2,4],[3,6]]，这些区间最多有两个区间互不相交，即[[1,3],[3,6]]，你的算法应该返回 2。注意边界相同并不算相交。比如你今天有好几个活动，每个活动都可以用区间[start,end]表示开始和结束的时间，请问你今天最多能参加几个活动呢？

思路：

    1、从区间集合 intvs 中选择一个区间 x，这个 x 是在当前所有区间中结束最早的（end 最小）。
    2、把所有与 x 区间相交的区间从区间集合 intvs 中删除。
    3、重复步骤 1 和 2，直到 intvs 为空为止。之前选出的那些 x 就是最大不相交子集。

实现：按每个区间的end数值升序排序

    步骤 1：由于我们预先按照end排了序，所以选择 x 是很容易的。关键在于，如何去除与 x 相交的区间，选择下一轮循环的 x 呢？

    由于我们事先排了序，不难发现所有与 x 相交的区间必然会与 x 的end相交；如果一个区间不想与 x 的end相交，它的start必须要大于（或等于）x 的end

```
def intervalSchedule(intvs):
    if not intvs:
        return 0
    intvs.sort(key=lambda x:x[1]) # 升序排序
    count=1 # 至少应该有1个区间不相交
    x_end=intvs[0][1] # 排序后，第一个区间就是x
    for interval in intvs:
        start=interval[0]
        if start >= x_end:
            count+=1
            x_end=interval[1] # 更新x
    return count
```

### 45.跳跃游戏 II

    链接：https://leetcode-cn.com/problems/jump-game-ii/

    给定一个非负整数数组，你最初位于数组的第一个位置。

    数组中的每个元素代表你在该位置可以跳跃的最大长度。

    你的目标是使用最少的跳跃次数到达数组的最后一个位置。

    示例:

    输入: [2,3,1,1,4]
    输出: 2
    解释: 跳到最后一个位置的最小跳跃数是 2。
         从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。
    说明:

    假设你总是可以到达数组的最后一个位置。

参考：https://mp.weixin.qq.com/s?__biz=Mzg2NzA4MTkxNQ==&mid=2247486128&idx=2&sn=bcec6b9eb7374169d823963bb7ca8415&scene=21#wechat_redirect

问题是：保证你一定可以跳到最后一格，请问你最少要跳多少次，才能跳过去？

题解一|从右向左搜索：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/greepy2.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/greepy2.png)

时间复杂度为 O(n^2)，空间复杂度为 O(1)
```
class Solution:
    def jump(self, nums: List[int]) -> int:
        right=len(nums)-1
        sum=0
        while right>0:
            cur=right-1
            for i in range(right-2,-1,-1):
                if (i+nums[i]) >= right:
                    cur=i
            right=cur
            sum+=1
        return sum
```

题解二|自顶向下的动态规划：

```
vector<int> memo;
// 主函数
int jump(vector<int>& nums) {
    int n = nums.size();
    // 备忘录都初始化为 n，相当于 INT_MAX
    // 因为从 0 调到 n - 1 最多 n - 1 步
    memo = vector<int>(n, n);
    return dp(nums, 0);
}

int dp(vector<int>& nums, int p) {
    int n = nums.size();
    // base case
    if (p >= n - 1) {
        return 0;
    }
    // 子问题已经计算过
    if (memo[p] != n) {
        return memo[p];
    }
    int steps = nums[p];
    // 你可以选择跳 1 步，2 步...
    for (int i = 1; i <= steps; i++) {
        // 穷举每一个选择
        // 计算每一个子问题的结果
        int subProblem = dp(nums, p + i);
        // 取其中最小的作为最终结果
        memo[p] = min(memo[p], subProblem + 1);
    }
    return memo[p];
}
```

题解三|贪心:

贪心选择性质，我们不需要「递归地」计算出所有选择的具体结果然后比较求最值，而只需要做出那个最有「潜力」，看起来最优的选择即可。

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/greepy1.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/greepy1.png)

时间复杂度是 O(n)，空间复杂度是 O(1)。

```
class Solution:
    def jump(self, nums: List[int]) -> int:
        if len(nums)<2:
            return 0
        sum,end,maxDistance=0,0,0
        # end代表能跳到的最远距离，maxDistance代表下一步能跳到的最远距离。
        for i in range(len(nums)-1):
            maxDistance=max(maxDistance,i+nums[i])
            if i==end:
                end=maxDistance
                sum+=1
        return sum
```

### 55.跳跃游戏
    链接：https://leetcode-cn.com/problems/jump-game/

    给定一个非负整数数组，你最初位于数组的第一个位置。

    数组中的每个元素代表你在该位置可以跳跃的最大长度。

    判断你是否能够到达最后一个位置。

    示例 1:

    输入: [2,3,1,1,4]
    输出: true
    解释: 我们可以先跳 1 步，从位置 0 到达 位置 1, 然后再从位置 1 跳 3 步到达最后一个位置。
    示例 2:

    输入: [3,2,1,0,4]
    输出: false
    解释: 无论怎样，你总会到达索引为 3 的位置。但该位置的最大跳跃长度是 0 ， 所以你永远不可能到达最后一个位置。

题解一：
```
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        right=len(nums)-1
        for i in range(len(nums)-2,-1,-1):
            if (i+nums[i]) >= right:
                right=i
        if right==0:
            return True
        return False
```

题解二|贪心：

思考：
    通过题目中的跳跃规则，最多能跳多远？如果能够越过最后一格，返回 true，否则返回 false。

```
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        n=len(nums)
        maxDistance=0
        for i in range(n-1):
            maxDistance=max(maxDistance,i+nums[i])
            if maxDistance <= i:
                return False
        return maxDistance >= n-1
```


```
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        if len(nums)<2:
            return True
        end,maxDistance=0,0
        # end代表能跳到的最远距离，maxDistance代表下一步能跳到的最远距离。
        for i in range(len(nums)-1): # for i in range(len(nums)):
            maxDistance=max(maxDistance,i+nums[i])
            if i==end:
                end=maxDistance
            if end >= len(nums)-1:
                return True
        return False
```

### 135.分发糖果
    链接：https://leetcode-cn.com/problems/candy/

    老师想给孩子们分发糖果，有 N 个孩子站成了一条直线，老师会根据每个孩子的表现，预先给他们评分。

    你需要按照以下要求，帮助老师给这些孩子分发糖果：

    每个孩子至少分配到 1 个糖果。
    相邻的孩子中，评分高的孩子必须获得更多的糖果。
    那么这样下来，老师至少需要准备多少颗糖果呢？

    示例 1:

    输入: [1,0,2]
    输出: 5
    解释: 你可以分别给这三个孩子分发 2、1、2 颗糖果。
    示例 2:

    输入: [1,2,2]
    输出: 4
    解释: 你可以分别给这三个孩子分发 1、2、1 颗糖果。
         第三个孩子只得到 1 颗糖果，这已满足上述两个条件。

题解一：
https://leetcode-cn.com/problems/candy/solution/candy-cong-zuo-zhi-you-cong-you-zhi-zuo-qu-zui-da-/
```
class Solution:
    def candy(self, ratings: List[int]) -> int:
        left=[1 for _ in range(len(ratings))]
        right=left[:]
        for i in range(1,len(ratings)):
            if ratings[i]>ratings[i-1]:
                left[i]=left[i-1]+1
        count=left[-1] # 从右向左遍历时，最后一个元素没有比较大小，所以此处计算count。
        for i in range(len(ratings)-2,-1,-1):
            if ratings[i]>ratings[i+1]:
                right[i]=right[i+1]+1
            count+=max(left[i],right[i])
        return count
```
题解二：
https://leetcode-cn.com/problems/candy/solution/fen-fa-tang-guo-by-powcai/
### 316.去除重复字母
    链接：https://leetcode-cn.com/problems/remove-duplicate-letters/

    给定一个仅包含小写字母的字符串，去除字符串中重复的字母，使得每个字母只出现一次。需保证返回结果的字典序最小（要求不能打乱其他字符的相对位置）。

    示例 1:

    输入: "bcabc"
    输出: "abc"
    示例 2:

    输入: "cbacdcbc"
    输出: "acdb"

名词解释：

    字典序：对于字符而言，按 ascii 码值进行比较，小的排在前，大的排在后。对于字符串，从第 0 位字符开始比较，ascii 码数值小的排在前面，如果相同，就延后一位比较 ascii 码值大小。

    子序列：子序列不同于子串，子串要求它们在原串中连续，而子序列则不要求连续。例如acd是abcd的子序列，但不是子串。
?题解一（递归）：
```
class Solution:
    def removeDuplicateLetters(self, s: str) -> str:
        for i in sorted(set(s)):
            tmp=s[s.index(i):]
            if len(set(tmp)) == len(set(s)):
                return i+self.removeDuplicateLetters(tmp.replace(i,''))
        return ''
```
?题解二（迭代）：
```
class Solution:
    def removeDuplicateLetters(self, s: str) -> str:
        res = ""
        while s:
            # 从右往左找，找到最小位置的索引号
            loc = min(map(s.rindex, s))
            # 找该索引前面最小的字母
            a = min(s[:loc + 1])
            res += a
            s = s[s.index(a):].replace(a, "")
        return res
```
题解三(栈):
https://mp.weixin.qq.com/s?__biz=Mzg2NzA4MTkxNQ==&mid=2247486367&idx=2&sn=b25c0701241325f644acda12599ec06a&scene=21#wechat_redirect
```
class Solution:
    def removeDuplicateLetters(self, s: str) -> str:
        from collections import Counter
        count=Counter(s)
        stack=[]
        existed=set()
        for i in s:
            if i not in existed:
                while stack and stack[-1]>i and count[stack[-1]]>0:
                    existed.remove(stack.pop())
                stack.append(i)
                existed.add(i)
            count[i]-=1
        return ''.join(stack)
```

### 435.无重叠区间
    链接:https://leetcode-cn.com/problems/non-overlapping-intervals/

    给定一个区间的集合，找到需要移除区间的最小数量，使剩余区间互不重叠。

    注意:

    可以认为区间的终点总是大于它的起点。
    区间 [1,2] 和 [2,3] 的边界相互“接触”，但没有相互重叠。
    示例 1:

    输入: [ [1,2], [2,3], [3,4], [1,3] ]

    输出: 1

    解释: 移除 [1,3] 后，剩下的区间没有重叠。
    示例 2:

    输入: [ [1,2], [1,2], [1,2] ]

    输出: 2

    解释: 你需要移除两个 [1,2] 来使剩下的区间没有重叠。
    示例 3:

    输入: [ [1,2], [2,3] ]

    输出: 0

    解释: 你不需要移除任何区间，因为它们已经是无重叠的了。

已经会求最多有几个区间不会重叠了，那么剩下的不就是至少需要去除的区间吗？

```
def eraseOverlapIntervals(intervals) {
    n=len(intervals)
    return n - intervalSchedule(intervals);
}
```

题解一（贪心）:
    
    先计算能组成不重叠最多区间个数： 
    （1）按区间结尾排序
    （2）每次选择结尾最小，且与前一个区间不重叠

```
class Solution:
    def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
        if not intervals:
            return 0
        intervals.sort(key=lambda x:x[1])

        curr=0
        count=1
        for i in range(1,len(intervals)):
            if intervals[i][0]>=intervals[curr][1]:
                curr=i
                count+=1
        return len(intervals)-count
```

### 452. 用最少数量的箭引爆气球
    链接：https://leetcode-cn.com/problems/minimum-number-of-arrows-to-burst-balloons/

    在二维空间中有许多球形的气球。对于每个气球，提供的输入是水平方向上，气球直径的开始和结束坐标。由于它是水平的，所以y坐标并不重要，因此只要知道开始和结束的x坐标就足够了。开始坐标总是小于结束坐标。平面内最多存在104个气球。

    一支弓箭可以沿着x轴从不同点完全垂直地射出。在坐标x处射出一支箭，若有一个气球的直径的开始和结束坐标为 xstart，xend， 且满足  xstart ≤ x ≤ xend，则该气球会被引爆。可以射出的弓箭的数量没有限制。 弓箭一旦被射出之后，可以无限地前进。我们想找到使得所有气球全部被引爆，所需的弓箭的最小数量。

    Example:

    输入:
    [[10,16], [2,8], [1,6], [7,12]]

    输出:
    2

    解释:
    对于该样例，我们可以在x = 6（射爆[2,8],[1,6]两个气球）和 x = 11（射爆另外两个气球）。

思考：

只是有一点不一样，在intervalSchedule算法中，如果两个区间的边界触碰，不算重叠；而按照这道题目的描述，箭头如果碰到气球的边界气球也会爆炸，所以说相当于区间的边界触碰也算重叠。

```
class Solution:
    def findMinArrowShots(self, points: List[List[int]]) -> int:
        if not points:
            return 0
        points.sort(key=lambda x:x[1])
        n=len(points)
        count=1
        x_end=points[0][1]
        for point in points:
            start=point[0]
            if start > x_end:
                count+=1
                x_end=point[1]
        return count
```