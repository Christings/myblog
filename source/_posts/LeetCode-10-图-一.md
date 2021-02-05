---
title: LeetCode-10-图-一
date: 2020-12-08 10:04:32
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
### 207. 课程表
    链接：https://leetcode-cn.com/problems/course-schedule/

    你这个学期必须选修 numCourse 门课程，记为 0 到 numCourse-1 。

    在选修某些课程之前需要一些先修课程。 例如，想要学习课程 0 ，你需要先完成课程 1 ，我们用一个匹配来表示他们：[0,1]

    给定课程总量以及它们的先决条件，请你判断是否可能完成所有课程的学习？


    示例 1:

    输入: 2, [[1,0]] 
    输出: true
    解释: 总共有 2 门课程。学习课程 1 之前，你需要完成课程 0。所以这是可能的。
    示例 2:

    输入: 2, [[1,0],[0,1]]
    输出: false
    解释: 总共有 2 门课程。学习课程 1 之前，你需要先完成​课程 0；并且学习课程 0 之前，你还应先完成课程 1。这是不可能的。
    
    提示：

    输入的先决条件是由 边缘列表 表示的图形，而不是 邻接矩阵 。详情请参见图的表示法。
    你可以假定输入的先决条件中没有重复的边。
    1 <= numCourses <= 10^5

题解一|拓扑排序|bfs：

有向无环图，把一个 有向无环图 转成 线性的排序 就叫 拓扑排序。

有向图有 入度 和 出度 的概念：
    
    如果存在一条有向边 A --> B，则这条边给 A 增加了 1 个出度，给 B 增加了 1 个入度。

思路：

    A-->B
    每次选择入度为0的课程，因为它不需要依赖其他的课程；
    当选择A后，B的依赖课程少了一门，入度由1变为0，接着选B；
    一直选择，直到选不到入度为0的课程；

```
import collections

class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        n=len(prerequisites)
        if n==0:
            return True
        in_degrees=[0 for _ in range(numCourses)]
        adjacency=[[] for _ in range(numCourses)]
        queue=collections.deque()

        for second,first in prerequisites:
            in_degrees[second]+=1 # 节点的入度，index代表节点，value代表入度值。
            adjacency[first].append(second) # 邻接矩阵关系

        for i in range(numCourses):
            if in_degrees[i]==0:
                queue.append(i) # 如果入度为0，则把index即节点加入到queue队列中。
        
        while queue:
            node=queue.popleft()
            for i in adjacency[node]: # 节点指向的节点
                in_degrees[i]-=1
                if in_degrees[i]==0:
                    queue.append(i)
            numCourses-=1
        return numCourses==0
```

题解二|dfs：
```
import collections

class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        def dfs(i, adjacency, flags):
            if flags[i] == -1: return True
            if flags[i] == 1: return False
            flags[i] = 1
            for j in adjacency[i]:
                if not dfs(j, adjacency, flags): return False
            flags[i] = -1
            return True

        adjacency = [[] for _ in range(numCourses)]
        flags = [0 for _ in range(numCourses)]
        for cur, pre in prerequisites:
            adjacency[pre].append(cur)
        for i in range(numCourses):
            if not dfs(i, adjacency, flags): return False
        return True
```

### 997. 找到小镇的法官
    链接：https://leetcode-cn.com/problems/find-the-town-judge/

    在一个小镇里，按从 1 到 N 标记了 N 个人。传言称，这些人中有一个是小镇上的秘密法官。

    如果小镇的法官真的存在，那么：

    小镇的法官不相信任何人。
    每个人（除了小镇法官外）都信任小镇的法官。
    只有一个人同时满足属性 1 和属性 2 。
    给定数组 trust，该数组由信任对 trust[i] = [a, b] 组成，表示标记为 a 的人信任标记为 b 的人。

    如果小镇存在秘密法官并且可以确定他的身份，请返回该法官的标记。否则，返回 -1。

    示例 1：

    输入：N = 2, trust = [[1,2]]
    输出：2
    示例 2：

    输入：N = 3, trust = [[1,3],[2,3]]
    输出：3
    示例 3：

    输入：N = 3, trust = [[1,3],[2,3],[3,1]]
    输出：-1
    示例 4：

    输入：N = 3, trust = [[1,2],[2,3]]
    输出：-1
    示例 5：

    输入：N = 4, trust = [[1,3],[1,4],[2,3],[2,4],[4,3]]
    输出：3
    

    提示：

    1 <= N <= 1000
    trust.length <= 10000
    trust[i] 是完全不同的
    trust[i][0] != trust[i][1]
    1 <= trust[i][0], trust[i][1] <= N

题解一|出度和入度：
```
class Solution:
    def findJudge(self, N: int, trust: List[List[int]]) -> int:
        if N<1:
            return -1
        n=len(trust)
        indegree=[0 for _ in range(N+1)]
        outdegree=[0 for _ in range(N+1)]
        for first,second in trust:
            indegree[second]+=1
            outdegree[first]+=1
        for i in range(1,N+1): # 从1开始
            if indegree[i]==N-1 and outdegree[i]==0:
                return i
        return -1
```

```
class Solution:
    def findJudge(self, N: int, trust: List[List[int]]) -> int:
        if N<=1:
            return N
        n=len(trust)
        degree=[0 for _ in range(N+1)]
        for first,second in trust:
            degree[first]-=1
            degree[second]+=1
        for i in range(N+1):
            if degree[i]==N-1:
                return i
        return -1
```