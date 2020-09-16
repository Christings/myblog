---
title: LeetCode-3-树-三-路径总和专题
date: 2020-05-08 11:41:24
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
### 112.路径总和
    链接：https://leetcode-cn.com/problems/path-sum/

    给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。

    说明: 叶子节点是指没有子节点的节点。

    示例: 
    给定如下二叉树，以及目标和 sum = 22，

                  5
                 / \
                4   8
               /   / \
              11  13  4
             /  \      \
            7    2      1
    返回 true, 因为存在目标和为 22 的根节点到叶子节点的路径 5->4->11->2。

题解一|递归:

时间复杂度：我们访问每个节点一次，时间复杂度为 O(N) ，其中 N 是节点个数。
空间复杂度：最坏情况下，整棵树是非平衡的，例如每个节点都只有一个孩子，递归会调用 N 次（树的高度），因此栈的空间开销是 O(N) 。但在最好情况下，树是完全平衡的，高度只有 log(N)，因此在这种情况下空间复杂度只有 O(log(N)) 。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def hasPathSum(self, root: TreeNode, sum: int) -> bool:
        if not root:
            return False
        sum-=root.val # 不能交换顺序
        if not root.left and not root.right:
            return sum==0
        return self.hasPathSum(root.left,sum) or self.hasPathSum(root.right,sum)
```

题解二|迭代:

时间复杂度：和递归方法相同是 O(N)。
空间复杂度：当树不平衡的最坏情况下是 O(N) 。在最好情况（树是平衡的）下是 O(logN)。

```
class Solution:
    def hasPathSum(self, root: TreeNode, sum: int) -> bool:
        if not root:
            return False
        
        stack=[(root,sum-root.val)]
        while stack:
            node,curr=stack.pop()
            if not node.left and not node.right and curr==0:
                return True
            if node.left: 
                stack.append((node.left,curr-node.left.val))
            if node.right:
                stack.append((node.right,curr-node.right.val))
        return False
```

### 113. 路径总和 II
    链接：https://leetcode-cn.com/problems/path-sum-ii/

    给定一个二叉树和一个目标和，找到所有从根节点到叶子节点路径总和等于给定目标和的路径。

    说明: 叶子节点是指没有子节点的节点。

    示例:
    给定如下二叉树，以及目标和 sum = 22，

                  5
                 / \
                4   8
               /   / \
              11  13  4
             /  \    / \
            7    2  5   1
    返回:

    [
       [5,4,11,2],
       [5,8,4,5]
    ]

题解一|递归:
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def pathSum(self, root: TreeNode, sum: int) -> List[List[int]]:
        paths=[]
        def helper(root,sum,path=[]):
            if not root:
                return
            if not root.left and not root.right and sum-root.val==0:
                path+=[root.val]
                paths.append(path)

            helper(root.left,sum-root.val,path+[root.val])
            helper(root.right,sum-root.val,path+[root.val])
        helper(root,sum)
        return paths
```

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def pathSum(self, root: TreeNode, sum: int) -> List[List[int]]:
        res=[]
        def helper(root,sum,path):
            if not root:
                return
            if not root.left and not root.right and sum-root.val==0:
                path+=[root.val]
                res.append(path)
            sum-=root.val
            helper(root.left,sum,path+[root.val])
            helper(root.right,sum,path+[root.val])
        helper(root,sum,[])
        return res
```

?题解二|迭代|超时：

```
class Solution:
    def pathSum(self, root: TreeNode, sum: int) -> List[List[int]]:
        if not root:
            return []
        res=[]
        stack=[(root,[root.val])]
        while stack:
            node,curr=stack.pop()
            if not node.left and not node.right and sum(curr)==sum:
                res.append(curr)
            if node.left:
                stack.append((root.left,curr+[root.left.val]))
            if node.right:
                stack.append((root.right,curr+[root.right.val]))
        return res
```

### 437. 路径总和 III
    链接：https://leetcode-cn.com/problems/path-sum-iii/

    给定一个二叉树，它的每个结点都存放着一个整数值。

    找出路径和等于给定数值的路径总数。

    路径不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。

    二叉树不超过1000个节点，且节点数值范围是 [-1000000,1000000] 的整数。

    示例：

    root = [10,5,-3,3,2,null,11,3,-2,null,1], sum = 8

          10
         /  \
        5   -3
       / \    \
      3   2   11
     / \   \
    3  -2   1

    返回 3。和等于 8 的路径有:

    1.  5 -> 3
    2.  5 -> 2 -> 1
    3.  -3 -> 11

题解一|递归：

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def pathSum(self, root: TreeNode, sum: int) -> int:
        def helper(root,sum):
            if not root:
                return 0
            res=0
            if sum-root.val == 0:
                res+=1
            res+=helper(root.left,sum-root.val)
            res+=helper(root.right,sum-root.val)
            return res

        if not root:
            return 0
        return helper(root,sum)+self.pathSum(root.left,sum)+self.pathSum(root.right,sum)
```

