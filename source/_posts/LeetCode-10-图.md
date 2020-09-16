---
title: LeetCode-10-图
date: 2020-02-17 14:55:49
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---

## 十、图
### ???133.克隆图
    链接：https://leetcode-cn.com/problems/clone-graph/

    给你无向 连通 图中一个节点的引用，请你返回该图的 深拷贝（克隆）。

    图中的每个节点都包含它的值 val（int） 和其邻居的列表（list[Node]）。

    class Node {
        public int val;
        public List<Node> neighbors;
    }

题解一(dfs)：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, val = 0, neighbors = []):
        self.val = val
        self.neighbors = neighbors
"""
class Solution:
    def cloneGraph(self, node: 'Node') -> 'Node':
        lookup={}
        def dfs(node):
            # print(node.val)
            if not node:
                return
            if node in lookup:
                return lookup[node]
            clone=Node(node.val,[])
            lookup[node]=clone
            for n in node.neighbors:
                clone.neighbors.append(dfs(n))
            return clone
        return dfs(node)
```
