---
title: LeetCode-3-树-二
date: 2020-05-08 11:41:24
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
### 面试题 04.10. 检查子树
    链接：https://leetcode-cn.com/problems/check-subtree-lcci/

    检查子树。你有两棵非常大的二叉树：T1，有几万个节点；T2，有几万个节点。设计一个算法，判断 T2 是否为 T1 的子树。

    如果 T1 有这么一个节点 n，其子树与 T2 一模一样，则 T2 为 T1 的子树，也就是说，从节点 n 处把树砍断，得到的树与 T2 完全相同。

    示例1:

     输入：t1 = [1, 2, 3], t2 = [2]
     输出：true
    示例2:

     输入：t1 = [1, null, 2, 4], t2 = [3, 2]
     输出：false
    提示：

    树的节点数目范围为[0, 20000]。

题解一|递归：

    一：在 t1 中找到 t2 的起点。先判断 t1 当前节点，如果不对就判断 t1 左子树和 t1 右子树。
    二：从找到的起点开始判断剩下的点，t1 和 t2 同步左右子树搜索。


```
class Solution:
    def checkSubTree(self, t1: TreeNode, t2: TreeNode) -> bool:

        def helper(t1,t2):
            if not t2:
                return True
            elif not t1 and t2:
                return False
            elif t1.val != t2.val:
                return False
            else:
                return helper(t1.left,t2.left) and helper(t1.right,t2.right)

        if not  t1:
            return False
        if not t2:
            return True
        return helper(t1,t2) or self.checkSubTree(t1.left,t2) or self.checkSubTree(t1.right,t2)
```

```
class Solution:
    def checkSubTree(self, t1: TreeNode, t2: TreeNode) -> bool:
        if not  t1:
            return False
        if not t2:
            return True
        return self.helper(t1,t2) or self.checkSubTree(t1.left,t2) or self.checkSubTree(t1.right,t2)
    
    def helper(self,t1,t2):
        if not t2:
            return True
        elif not t1 and t2:
            return False
        elif t1.val != t2.val:
            return False
        else:
            return self.helper(t1.left,t2.left) and self.helper(t1.right,t2.right)
```


```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def isSubStructure(self, A: TreeNode, B: TreeNode) -> bool:
        if not A or not B:
            return False
        return self.helper(A,B) or self.isSubStructure(A.left,B) or self.isSubStructure(A.right,B)
    
    def helper(self,A,B):
        if not A and B:
            return False
        elif not B:
            return True
        elif A.val != B.val:
            return False
        else:
            return self.helper(A.left,B.left) and self.helper(A.right,B.right)
```

```
class Solution:
    def isSubStructure(self, A: TreeNode, B: TreeNode) -> bool:
        def helper(A,B):
            if not B:
                return True
            if not A or (A.val != B.val):
                return False
            return helper(A.left,B.left) and helper(A.right,B.right)
        return bool(A and B) and (helper(A,B) or self.isSubStructure(A.left,B) or self.isSubStructure(A.right,B))
```

### 面试题 17.12. BiNode
    链接：https://leetcode-cn.com/problems/binode-lcci/

    二叉树数据结构TreeNode可用来表示单向链表（其中left置空，right为下一个链表节点）。实现一个方法，把二叉搜索树转换为单向链表，要求值的顺序保持不变，转换操作应是原址的，也就是在原始的二叉搜索树上直接修改。

    返回转换后的单向链表的头节点。

    注意：本题相对原题稍作改动

    示例：

    输入： [4,2,5,1,3,null,6,0]
    输出： [0,null,1,null,2,null,3,null,4,null,5,null,6]
    提示：

    节点数量不会超过 100000。

题解一|迭代|中序遍历：

    root.left=None
    pre.right=root
    pre=root

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def convertBiNode(self, root: TreeNode) -> TreeNode:
        head=TreeNode(0)
        pre=head
        stack=[]
        while stack or root:
            if root:
                stack.append(root)
                root=root.left
            else:
                root=stack.pop()

                root.left=None
                pre.right=root
                pre=root

                root=root.right
        return head.right
```

题解二|递归:
```
class Solution:
    def convertBiNode(self, root: TreeNode) -> TreeNode:
        head=TreeNode(0)
        self.helper(root,head)
        return head.right
    
    def helper(self,root,pre):
        if root:
            pre=self.helper(root.left,pre)
            root.left=None
            pre.right=root
            pre=root
            pre=self.helper(root.right,pre)
        return pre
```

```
class Solution:
    def convertBiNode(self, root: TreeNode) -> TreeNode:
        head=TreeNode(0)
        self.pre=head
        self.helper(root)
        return head.right

    def helper(self,root):
        if not root:
            return
        self.helper(root.left)

        root.left=None
        self.pre.right=root
        self.pre=root

        self.helper(root.right)
```

### 面试题 04.12. 求和路径
    链接：https://leetcode-cn.com/problems/paths-with-sum-lcci/

    给定一棵二叉树，其中每个节点都含有一个整数数值(该值或正或负)。设计一个算法，打印节点数值总和等于某个给定值的所有路径的数量。注意，路径不一定非得从二叉树的根节点或叶节点开始或结束，但是其方向必须向下(只能从父节点指向子节点方向)。

    示例:
    给定如下二叉树，以及目标和 sum = 22，

                  5
                 / \
                4   8
               /   / \
              11  13  4
             /  \    / \
            7    2  5   1
    返回:

    3
    解释：和为 22 的路径有：[5,4,11,2], [5,8,4,5], [4,11,7]
    提示：

    节点总数 <= 10000

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
        if not root:
            return 0
        return self.helper(root,sum)+self.pathSum(root.left,sum)+self.pathSum(root.right,sum)

    def helper(self,root,sum):
        count=0
        if not root:
            return 0
        if sum-root.val==0:
            count+=1
        count+=self.helper(root.left,sum-root.val)
        count+=self.helper(root.right,sum-root.val)
        return count
```

### 剑指 Offer 36. 二叉搜索树与双向链表
    链接：https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/

    输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表。要求不能创建任何新的节点，只能调整树中节点指针的指向。

    为了让您更好地理解问题，以下面的二叉搜索树为例：


    我们希望将这个二叉搜索树转化为双向循环链表。链表中的每个节点都有一个前驱和后继指针。对于双向循环链表，第一个节点的前驱是最后一个节点，最后一个节点的后继是第一个节点。

    下图展示了上面的二叉搜索树转化成的链表。“head” 表示指向链表中有最小元素的节点。

    特别地，我们希望可以就地完成转换操作。当转化完成以后，树中节点的左指针需要指向前驱，树中节点的右指针需要指向后继。还需要返回链表中的第一个节点的指针。

    注意：本题与主站 426 题相同：https://leetcode-cn.com/problems/convert-binary-search-tree-to-sorted-doubly-linked-list/

    注意：此题对比原题有改动。

题解一|中序遍历：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, val, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
"""
class Solution:
    def __init__(self):
        self.head=None
        self.pre=None
    
    def treeToDoublyList(self, root: 'Node') -> 'Node':
        if not root:
            return root
        self.dfs(root)
        self.head.left,self.pre.right=self.pre,self.head
        return self.head
       
    def dfs(self,root):
        if not root:
            return
        self.dfs(root.left)
        if not self.pre:
            self.head=root
        else:
            self.pre.right=root
            root.left=self.pre
        self.pre=root
        self.dfs(root.right)
```
### 面试题54. 二叉搜索树的第k大节点
    链接：https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/

    给定一棵二叉搜索树，请找出其中第k大的节点。

    示例 1:

    输入: root = [3,1,4,null,2], k = 1
       3
      / \
     1   4
      \
       2
    输出: 4
    示例 2:

    输入: root = [5,3,6,2,4,null,null,1], k = 3
           5
          / \
         3   6
        / \
       2   4
      /
     1
    输出: 4
     
    限制：

    1 ≤ k ≤ 二叉搜索树元素个数

注意：和二叉搜索树的第k小节点的区别。

题解一|迭代：

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def kthLargest(self, root: TreeNode, k: int) -> int:
        if not root:
            return 0
        stack=[]
        while stack or root:
            if root:
                stack.append(root)
                root=root.right
            else:
                root=stack.pop()
                k-=1
                if k==0:
                    return root.val
                root=root.left
```

题解二|递归:
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def kthLargest(self, root: TreeNode, k: int) -> int:
        self.k=k
        self.res=0
        self.helper(root)
        return self.res
    def helper(self,root):
        if not root:
            return 0
        self.helper(root.right)
        if self.k==0: return # ？？提高性能，提前返回。
        self.k-=1        
        if self.k==0:
            self.res=root.val # 直接return不行，需要存入全局变量中
        self.helper(root.left)
        return self.res
```

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def kthLargest(self, root: TreeNode, k: int) -> int:
        self.k=k
        self.res=0
        self.helper(root)
        return self.res
    def helper(self,root):
        if not root or self.k == 0:
            return 0
        self.helper(root.right)
        self.k-=1        
        if self.k==0:
            self.res=root.val # 直接return不行，需要存入全局变量中
        self.helper(root.left)
        return self.res
```

### 面试题55 - I. 二叉树的深度
    链接：https://leetcode-cn.com/problems/er-cha-shu-de-shen-du-lcof/

    输入一棵二叉树的根节点，求该树的深度。从根节点到叶节点依次经过的节点（含根、叶节点）形成树的一条路径，最长路径的长度为树的深度。

    例如：

    给定二叉树 [3,9,20,null,null,15,7]，

        3
       / \
      9  20
        /  \
       15   7
    返回它的最大深度 3 。

    提示：节点总数 <= 10000

题解一|递归：
```
class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        if not root:
            return 0
        left=self.maxDepth(root.left)
        right=self.maxDepth(root.right)
        return max(left,right)+1
```

题解二|迭代:
```
class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        if not root:
            return 0
        stack=[root]
        depth=0
        while stack:
            tmp=[]
            for i in range(len(stack)):
                root=stack.pop()
                if root.left:
                    tmp.append(root.left)
                if root.right:
                    tmp.append(root.right)
            stack=tmp
            depth+=1
        return depth
```

```
class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        if not root:
            return 0
        stack=[root]
        depth=0
        while stack:
            tmp=[]
            for root in stack:
                if root.left:
                    tmp.append(root.left)
                if root.right:
                    tmp.append(root.right)
            stack=tmp
            depth+=1
        return depth
```

### 面试题55 - II. 平衡二叉树
    链接：https://leetcode-cn.com/problems/ping-heng-er-cha-shu-lcof/

    输入一棵二叉树的根节点，判断该树是不是平衡二叉树。如果某二叉树中任意节点的左右子树的深度相差不超过1，那么它就是一棵平衡二叉树。

    示例 1:

    给定二叉树 [3,9,20,null,null,15,7]

        3
       / \
      9  20
        /  \
       15   7
    返回 true 。

    示例 2:

    给定二叉树 [1,2,2,3,3,null,null,4,4]

           1
          / \
         2   2
        / \
       3   3
      / \
     4   4
    返回 false 。

限制：1 <= 树的结点个数 <= 10000

题解一（递归）：
```
class Solution:
    def isBalanced(self, root: TreeNode) -> bool:
        if not root:
            return True
        return abs(self.helper(root.left)-self.helper(root.right))<=1 and self.isBalanced(root.left) and self.isBalanced(root.right)
    
    def helper(self,root):
        if not root:
            return 0
        left=self.helper(root.left)
        right=self.helper(root.right)
        return max(left,right)+1
```

### 面试题68 - I. 二叉搜索树的最近公共祖先
    链接：https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/

    给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

    百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

    例如，给定如下二叉搜索树:  root = [6,2,8,0,4,7,9,null,null,3,5]

    示例 1:

    输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
    输出: 6 
    解释: 节点 2 和节点 8 的最近公共祖先是 6。
    示例 2:

    输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4
    输出: 2
    解释: 节点 2 和节点 4 的最近公共祖先是 2, 因为根据定义最近公共祖先节点可以为节点本身。
     

    说明:

    所有节点的值都是唯一的。
    p、q 为不同节点且均存在于给定的二叉搜索树中。

最近公共祖先（最近公共节点）:

    根据题目给定的前提：

        所有节点的值都是唯一的。
        p、q 为不同节点且均存在于给定的二叉搜索树中。

    说明有以下几种情况：

        二叉树本身为空，root == null ，return root
        p.val == q.val ,一个节点也可以是它自己的祖先
        p.val 和 q.val 都小于 root.val
        (两个子节点的值都小于根节点的值，说明它们的公共节点只能在二叉树的左子树寻找）
        p.val 和 q.val 都大于 root.val
        (两个子节点的值都大于根节点的值，说明它们的公共节点只能在二叉树的右子树寻找）
        如果上述的情况皆不满足，说明其公共节点既不在左子树也不在右子树上，只能为最顶端的公共节点，return root
        p.val < root.val && q.val > root.val 或 p.val > root.val && q.val < root.val

题解一|递归：
```
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        # if not root:
        #     return
        if root.val < p.val and root.val < q.val:
            return self.lowestCommonAncestor(root.right,p,q)
        elif root.val > p.val and root.val > q.val:
            return self.lowestCommonAncestor(root.left,p,q)
        return root
```

题解二|后序遍历：
```
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if not root or root==p or root==q:
            return root
        l=self.lowestCommonAncestor(root.left,p,q)
        r=self.lowestCommonAncestor(root.right,p,q)
        if not l:
            return r
        elif not r:
            return l
        else:
            return root
```

题解三|迭代：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        while root:
            if root.val < p.val and root.val < q.val:
                root=root.right
            elif root.val > p.val and root.val > q.val:
                root=root.left
            else:
                break
        return root
```

### 面试题68 - II. 二叉树的最近公共祖先
    链接：https://leetcode-cn.com/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/

    给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

    百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

    例如，给定如下二叉树:  root = [3,5,1,6,2,0,8,null,null,7,4]

    示例 1:

    输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
    输出: 3
    解释: 节点 5 和节点 1 的最近公共祖先是节点 3。
    示例 2:

    输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
    输出: 5
    解释: 节点 5 和节点 4 的最近公共祖先是节点 5。因为根据定义最近公共祖先节点可以为节点本身。
     

    说明:

    所有节点的值都是唯一的。
    p、q 为不同节点且均存在于给定的二叉树中。

题解一（后序遍历）：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
        if not root or root==p or root==q:
            return root
        l=self.lowestCommonAncestor(root.left,p,q)
        r=self.lowestCommonAncestor(root.right,p,q)
        if not l:
            return r
        elif not r:
            return l
        else:
            return root

```

```
class Solution:
    def lowestCommonAncestor(self, root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
        if not root or root == p or root == q: return root
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        if not left and not right: return # 1.
        if not left: return right # 3.
        if not right: return left # 4.
        return root # 2. if left and right:
```
