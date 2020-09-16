---
title: 数据结构与算法-3-BFS和DFS
date: 2020-06-05 14:38:46
tags: 数据结构与算法
categories:
        - 数据结构与算法

## 一、综述

    BFS 和 DFS 的最主要的区别是：BFS 找到的路径一定是最短的，但代价就是空间复杂度比 DFS大很多。


## 二、BFS
### 1、常见场景
    
    问题的本质就是让你在一幅「图」中找到从起点start到终点target的最近距离。
### 2、框架

```
// 计算从起点 start 到终点 target 的最近距离
int BFS(Node start, Node target) {
    Queue<Node> q; // 核心数据结构
    Set<Node> visited; // 避免走回头路

    q.offer(start); // 将起点加入队列
    visited.add(start);
    int step = 0; // 记录扩散的步数

    while (q not empty) {
        int sz = q.size();
        /* 将当前队列中的所有节点向四周扩散 */
        for (int i = 0; i < sz; i++) {
            Node cur = q.poll();
            /* 划重点：这里判断是否到达终点 */
            if (cur is target)
                return step;
            /* 将 cur 的相邻节点加入队列 */
            for (Node x : cur.adj())
                if (x not in visited) {
                    q.offer(x);
                    visited.add(x);
                }
        }
        /* 划重点：更新步数在这里 */
        step++;
    }
}
```

python模版:

```
# 计算从起点 start 到终点 target 的最近距离
from collections import deque
def BFS(start,target):
    queue=deque([n]) # 核心数据结构，将起点加入队列
    visited=set()  # 避免走回头路
    step=0 # 记录扩散的步数
    while queue:
        size=len(queue)
        # 将当前队列中的所有节点向四周扩散
        for i in range(size):
            cur=queue.pop()
            if cur == target: # 这里判断是否到达终点 
                return step
            for j in range(cur.XXX): # 将 cur 的相邻节点加入队列 
                if j not in visited:
                    queue.appendleft(j)
                    visited.add(j)
        step+=1 # 更新步数在这里

```

### 3、例子

    111. 二叉树的最小深度

```
from collections import deque
def minDepth(root):
    if not root:
        return 0
    queue=deque([root])
    depth=1
    while queue:
        size=len(queue)
        for i in range(size):
            cur=queue.pop()
            if not cur.left and not cur.right:
                return depth
            if cur.left:
                queue.appendleft(cur.left)
            if cur.right:
                queue.appendleft(cur.right)
        depth+=1
    return depth
```

    279. 完全平方数

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





