---
title: LeetCode-3.树-一
date: 2020-01-02 15:49:29
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
参考文章：https://www.cnblogs.com/bjwu/p/9284534.html

## 三、树
### 94.二叉树的中序遍历
    链接：https://leetcode-cn.com/problems/binary-tree-inorder-traversal/

    给定一个二叉树，返回它的中序 遍历。

    示例:

    输入: [1,null,2,3]
       1
        \
         2
        /
       3

    输出: [1,3,2]
    进阶: 递归算法很简单，你可以通过迭代算法完成吗？

题解一|递归：

时间复杂度：O(n)，其中 n 为二叉树节点的个数。二叉树的遍历中每个节点会被访问一次且只会被访问一次。
空间复杂度：O(n)。空间复杂度取决于递归的栈深度，而栈深度在二叉树为一条链的情况下会达到 O(n) 的级别。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def inorderTraversal(self, root: TreeNode) -> List[int]:
        res=[]
        if root:
            res=self.inorderTraversal(root.left)
            res.append(root.val)
            res+=self.inorderTraversal(root.right)
        return res
```

```
class Solution:
    def inorderTraversal(self, root):
        """
        :type root: TreeNode
        :rtype: List[int]
        """
        if not root:
            return [] 
        return self.inorderTraversal(root.left) + [root.val] + self.inorderTraversal(root.right)
```
题解二|迭代:

![inorder](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/inorder.png)

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def inorderTraversal(self, root: TreeNode) -> List[int]:
        res=[]
        stack=[]
        curr=root
        while stack or curr:
            if curr:
                stack.append(curr)
                curr=curr.left
            else:
                curr=stack.pop()
                res.append(curr.val)
                curr=curr.right
        return res
```
### ?95.不同的二叉搜索树 II   
    链接：https://leetcode-cn.com/problems/unique-binary-search-trees-ii/

    给定一个整数 n，生成所有由 1 ... n 为节点所组成的二叉搜索树。

    示例:

    输入: 3
    输出:
    [
      [1,null,3,2],
      [3,2,null,1],
      [3,1,null,null,2],
      [2,1,3],
      [1,null,2,null,3]
    ]
    解释:
    以上的输出对应以下 5 种不同结构的二叉搜索树：

       1         3     3      2      1
        \       /     /      / \      \
         3     2     1      1   3      2
        /     /       \                 \
       2     1         2                 3

题解一|递归：
![tree3](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/tree3.png)
![tree4](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/tree4.png)

    所以如果求 1...n 的所有可能。

    我们只需要把 1 作为根节点，[ ] 空作为左子树，[ 2 ... n ] 的所有可能作为右子树。

    2 作为根节点，[ 1 ] 作为左子树，[ 3...n ] 的所有可能作为右子树。

    3 作为根节点，[ 1 2 ] 的所有可能作为左子树，[ 4 ... n ] 的所有可能作为右子树，然后左子树和右子树两两组合。

    4 作为根节点，[ 1 2 3 ] 的所有可能作为左子树，[ 5 ... n ] 的所有可能作为右子树，然后左子树和右子树两两组合。

    ...

    n 作为根节点，[ 1... n ] 的所有可能作为左子树，[ ] 作为右子树。

    至于，[ 2 ... n ] 的所有可能以及 [ 4 ... n ] 以及其他情况的所有可能，可以利用上边的方法，把每个数字作为根节点，然后把所有可能的左子树和右子树组合起来即可。

    如果只有一个数字，那么所有可能就是一种情况，把该数字作为一棵树。而如果是 [ ]，那就返回 null。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def generateTrees(self, n: int) -> List[TreeNode]:
        if n:
            return self.build(1,n)
        else:
            []
    def build(self,start,end):
        # 结束条件
        if start>end:
            return [None]
        
        trees=[]
        for i in range(start,end+1):
            leftTrees=self.build(start,i-1)
            rightTrees=self.build(i+1,end)
            for l in leftTrees:
                for r in rightTrees:
                    currentTree=TreeNode(i)
                    currentTree.left=l
                    currentTree.right=r
                    trees.append(currentTree)
        # print(trees)
        return trees
```
### 96.不同的二叉搜索树
    链接：https://leetcode-cn.com/problems/unique-binary-search-trees/

    给定一个整数 n，求以 1 ... n 为节点组成的二叉搜索树有多少种？

    示例:

    输入: 3
    输出: 5
    解释:
    给定 n = 3, 一共有 5 种不同结构的二叉搜索树:

       1         3     3      2      1
        \       /     /      / \      \
         3     2     1      1   3      2
        /     /       \                 \
       2     1         2                 3

题解一|动态规划:

二叉树定义：

二叉查找树（Binary Search Tree），（又：二叉搜索树，二叉排序树）它或者是一棵空树，或者是具有下列性质的二叉树： 若它的左子树不空，则左子树上所有结点的值均小于它的根结点的值； 若它的右子树不空，则右子树上所有结点的值均大于它的根结点的值； 它的左、右子树也分别为二叉排序树。

参考：https://leetcode-cn.com/problems/unique-binary-search-trees/solution/bu-tong-de-er-cha-sou-suo-shu-by-leetcode/
方法：

    给定一个有序序列 1 ... n，为了根据序列构建一棵二叉搜索树。我们可以遍历每个数字 i，将该数字作为树根，1 ... (i-1) 序列将成为左子树，(i+1) ... n 序列将成为右子树。于是，我们可以递归地从子序列构建子树。
    在上述方法中，由于根各自不同，每棵二叉树都保证是独特的。

    可见，问题可以分解成规模较小的子问题。因此，我们可以存储并复用子问题的解，而不是递归的（也重复的）解决这些子问题，这就是动态规划法。

函数定义：
    
    令G(n)的从1到n可以形成二叉搜索树个数
    令f(i)为以i为根的二叉搜索树的个数

    所以G(n)是解决问题的函数：G(n) = f(1) + f(2) + f(3) + f(4) + ... + f(n)

![tree](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/tree.png)

1、定义数组元素的含义
    G[i]代表二叉搜索树的个数
2、找出数组之间的的关系式
    以i为根，[0,i-1]为左节点，[i+1,n]为右节点
    之后再对[0,i-1]和[i+1,n]递归求解

    f(i)=G(i−1)∗G(n−i)

    G(n)=G(0)∗G(n−1)+G(1)∗(n−2)+...+G(n−1)∗G(0)
3、找出初始值
    G[0]=1
    G[1]=1
    G[2]=2

时间复杂度：O(n^2)
空间复杂度：O(n)

dp[2] = f(1)+f(2)= dp[0] * dp[1] (1为根的情况) + dp[1] * dp[0] (2为根的情况)
```
class Solution:
    def numTrees(self, n: int) -> int:
        dp=[0]*(n+1)
        dp[0]=1
        dp[1]=1

        for i in range(2,n+1):
            for j in range(1,i+1):
                dp[i]+=dp[j-1]*dp[i-j] 
        # print(dp)
        return dp[n]
```
### 98.验证二叉搜索树
    链接：https://leetcode-cn.com/problems/validate-binary-search-tree/

    给定一个二叉树，判断其是否是一个有效的二叉搜索树。

    假设一个二叉搜索树具有如下特征：

    节点的左子树只包含小于当前节点的数。
    节点的右子树只包含大于当前节点的数。
    所有左子树和右子树自身必须也是二叉搜索树。
    示例 1:

    输入:
        2
       / \
      1   3
    输出: true
    示例 2:

    输入:
        5
       / \
      1   4
         / \
        3   6
    输出: false
    解释: 输入为: [5,1,4,null,null,3,6]。
         根节点的值为 5 ，但是其右子节点值为 4 。

https://leetcode-cn.com/problems/validate-binary-search-tree/solution/die-dai-yu-di-gui-by-powcai/

题解一|递归:

理解：

    当前节点的值是其左子树的值的上界（最大值）
    当前节点的值是其右子树的值的下界（最小值）

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        def helper(root,lower=float('-inf'),upper=float('inf')):
            if not root:
                return True
            val=root.val
            if val<=lower or val>=upper:
                return False
            
            if not helper(root.right,val,upper):
                return False

            if not helper(root.left,lower,val):
                return False
        
            return True
        return helper(root)
```

```
class Solution:
    def isValidBST(self, root: TreeNode, low = float('-inf'), high = float('inf')) -> bool:
        if not root:return True
        if not low<root.val<high:return False
        return self.isValidBST(root.left,low,root.val) and self.isValidBST(root.right,root.val,high)
```

题解二|迭代：
时间复杂度 : O(N)。每个结点访问一次。
空间复杂度 : O(N)。我们跟进了整棵树。
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        if not root:
            return True # 空树，否则过不了case[]
        stack=[(root,float('-inf'),float('inf')),]
        while stack:
            root,lower,upper=stack.pop()
            # print(lower,upper)
            if not root:
                continue
            val=root.val
            if val>=upper or val<=lower:
                return False
            stack.append((root.right,val,upper)) # 此处的left和right的顺序没有关系
            stack.append((root.left,lower,val))
        return True
```
题解三|中序遍历:
    中序遍历[1,2,3]，检查 inorder 中的每个元素是否小于下一个。

    时间复杂度 : 最坏情况下（树为二叉搜索树或破坏条件的元素是最右叶结点）为 O(N)。
    空间复杂度 : O(N) 用于存储 stack。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        stack=[]
        inorder=float('-inf')
        while stack or root:
            while root:
                stack.append(root)
                root=root.left
            root=stack.pop()
            if root.val <= inorder: # 注意小于号，否则[1,1]不能pass。
                return False
            inorder=root.val
            root=root.right
        return True
```

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        stack=[]
        inorder=float('-inf')
        while stack or root:
            if root:
                stack.append(root)
                root=root.left
            else:
                root=stack.pop()
                if root.val <= inorder:
                    return False
                inorder=root.val
                root=root.right
        return True
```

### 99.恢复二叉搜索树
    链接：https://leetcode-cn.com/problems/recover-binary-search-tree/

    二叉搜索树中的两个节点被错误地交换。

    请在不改变其结构的情况下，恢复这棵树。

    示例 1:

    输入: [1,3,null,null,2]

       1
      /
     3
      \
       2

    输出: [3,1,null,null,2]

       3
      /
     1
      \
       2
    示例 2:

    输入: [3,1,4,null,null,2]

      3
     / \
    1   4
       /
      2

    输出: [2,1,4,null,null,3]

      2
     / \
    1   4
       /
      3
    进阶:

    使用 O(n) 空间复杂度的解法很容易实现。
    你能想出一个只使用常数空间的解决方案吗？


思路:
    难点：找到2个节点进行交换

    二叉树搜索树的中序遍历(中序遍历遍历元素是递增的)

    中序遍历顺序是 1,3,2,4,我们只要找到节点3和节点2交换顺序即可!

    这里我们有个规律发现这两个节点:

        第一个节点,是第一个按照中序遍历时候前一个节点大于后一个节点,我们选取前一个节点,这里指节点3;

        第二个节点,是在第一个节点找到之后, 后面出现前一个节点大于后一个节点,我们选择后一个节点,这里指节点2;

题解一|递归：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def recoverTree(self, root: TreeNode) -> None:
        """
        Do not return anything, modify root in-place instead.
        """
        self.firstNode=None
        self.secondNode=None
        self.pre=TreeNode(float('-inf'))
        def helper(root):
            if not root:
                return
            helper(root.left)
            if self.firstNode is None and self.pre.val >= root.val:
                self.firstNode=self.pre
            if self.firstNode and self.pre.val >= root.val:
                self.secondNode=root
            self.pre=root
            helper(root.right)
        helper(root)
        self.firstNode.val,self.secondNode.val=self.secondNode.val,self.firstNode.val
```
题解二|迭代：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def recoverTree(self, root: TreeNode) -> None:
        """
        Do not return anything, modify root in-place instead.
        """
        firstNode=None
        secondNode=None
        pre=TreeNode(float('-inf'))
        stack=[]
        while stack or root:
            while root:
                stack.append(root)
                root=root.left
            root=stack.pop()

            if not firstNode and pre.val > root.val:
                firstNode=pre
            if firstNode and pre.val > root.val:
                secondNode=root
            pre=root
            root=root.right
        firstNode.val,secondNode.val=secondNode.val,firstNode.val
```

### 100.相同的树
    链接：https://leetcode-cn.com/problems/same-tree/

    给定两个二叉树，编写一个函数来检验它们是否相同。

    如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。

    示例 1:

    输入:       1         1
              / \       / \
             2   3     2   3

            [1,2,3],   [1,2,3]

    输出: true
    示例 2:

    输入:      1          1
              /           \
             2             2

            [1,2],     [1,null,2]

    输出: false
    示例 3:

    输入:       1         1
              / \       / \
             2   1     1   2

            [1,2,1],   [1,1,2]

    输出: false
题解一|递归:

时间复杂度 : O(N)，其中 N 是树的结点数，因为每个结点都访问一次。

空间复杂度 : 最优情况（完全平衡二叉树）时为 O(log(N))，最坏情况下（完全不平衡二叉树）时为 O(N)，用于维护递归栈。

```
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class Solution:
    def isSameTree(self, p: TreeNode, q: TreeNode) -> bool:
        if p is None and q is None:
            return True
        if p is None or q is None:
            return False
        if p.val != q.val:
            return False
        
        return self.isSameTree(p.left,q.left) and self.isSameTree(p.right,q.right)

```

```
class Solution:
    def isSameTree(self, p: TreeNode, q: TreeNode) -> bool:
        if p is None and q is None:
            return True
        if p is None or q is None:
            return False
        if p.val == q.val:
            return self.isSameTree(p.left,q.left) and self.isSameTree(p.right,q.right)
```

题解二|迭代:

时间复杂度 : O(N)，其中 N 是树的结点数，因为每个结点都访问一次。

空间复杂度 : 最优情况（完全平衡二叉树）时为 O(log(N))，最坏情况下（完全不平衡二叉树）时为 O(N)，用于维护双向队列。

```
class Solution:
    def isSameTree(self, p: TreeNode, q: TreeNode) -> bool:
        from collections import deque
        def helper(p,q):
            if not p and not q:
                return True
            if not p or not q:
                return False
            if p.val != q.val:
                return False
            return True
        deq=deque([(p,q),])
        while deq:
            p,q=deq.popleft()
            if not helper(p,q):
                return False
            # 此处改为if q也可以。
            if p:  # 当p和q为None时，此时p.left不成立，所以需要判断。
                deq.append((p.left,q.left))
                deq.append((p.right,q.right))
        return True
```

总结：第一种方法就是先序遍历的递归形式，第二种方法是层次遍历的非递归算法

### 101.对称二叉树
    链接：https://leetcode-cn.com/problems/symmetric-tree/
    给定一个二叉树，检查它是否是镜像对称的。

    例如，二叉树 [1,2,2,3,4,4,3] 是对称的。

        1
       / \
      2   2
     / \ / \
    3  4 4  3
    但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:

        1
       / \
      2   2
       \   \
       3    3
题解一|递归:
```
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class Solution:
    def isSymmetric(self, root: TreeNode) -> bool:
        if root is None:
            return True
        return self.isMirror(root.left,root.right)

    def isMirror(self,p,q):
        if p is None and q is None:
            return True
        if p is None or q is None:
            return False
        #if left.val != right.val:
        #    return False
        l=self.isMirror(p.left,q.right)
        r=self.isMirror(q.left,p.right) # 注意：此处是对称，注意节点的选择。
        return p.val==q.val and l and r
```

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def isSymmetric(self, root: TreeNode) -> bool:
        def helper(p,q):
            if not p and not q:
                return True
            if not p or not q:
                return False
            if p.val != q.val:
                return False
            return helper(p.left,q.right) and helper(p.right,q.left)
        if not root:
            return True
        return helper(root.left,root.right)
```

题解二|迭代：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def isSymmetric(self, root: TreeNode) -> bool:
        from collections import deque
        def helper(p,q):
            if not p and not q:
                return True
            if not p or not q:
                return False
            if p.val != q.val:
                return False
            return True
        deq=deque([(root,root),])
        while deq:
            p,q=deq.popleft()
            if not helper(p,q):
                return False
            if p:  # 当p和q为None时，此时p.left不成立，所以需要判断。
                deq.append((p.left,q.right))
                deq.append((p.right,q.left))
        return True
```

别人的迭代：

```
class Solution(object):
    def isSymmetric(self, root):
        if root == None:
            return True
        # 用队列保存节点
        queue = [root,root]
        while queue:
            # 从队列中取出两个节点，再比较这两个节点
            left = queue.pop()
            right = queue.pop()
            # 如果两个节点都为空就继续循环，两者有一个为空就返回false
            if(left == None and right == None):
                continue
            if(left == None or right == None):
                return False
            if left.val!=right.val:
                return False
            # 将左节点的左孩子， 右节点的右孩子放入队列
            queue.append(left.left)
            queue.append(right.right)
            # 将左节点的右孩子，右节点的左孩子放入队列
            queue.append(left.right)
            queue.append(right.left)
        return True
```

### 102.二叉树的层次遍历
    链接：https://leetcode-cn.com/problems/binary-tree-level-order-traversal/

    给定一个二叉树，返回其按层次遍历的节点值。 （即逐层地，从左到右访问所有节点）。

    例如:
    给定二叉树: [3,9,20,null,null,15,7],

        3
       / \
      9  20
        /  \
       15   7
    返回其层次遍历结果：

    [
      [3],
      [9,20],
      [15,7]
    ]
题解一|递归：

```
class Solution:

    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        res=[]
        
        def helper(node,depth):
            if not node:
                return
            if len(res)==depth:
                res.append([])
            res[depth].append(node.val)
            helper(node.left,depth+1)
            helper(node.right,depth+1)
        helper(root,0)
        return res
```
题解二|迭代：
    我们将树上顶点按照层次依次放入队列结构中，队列中元素满足 FIFO（先进先出）的原则。在 Java 中可以使用 Queue 接口中的 LinkedList实现。在 Python 中如果使用 Queue 结构，但因为它是为多线程之间安全交换而设计的，所以使用了锁，会导致性能不佳。因此在 Python 中可以使用 deque 的 append() 和 popleft() 函数来快速实现队列的功能。

    第 0 层只包含根节点 root ，算法实现如下：

        初始化队列只包含一个节点 root 和层次编号 0 ： level = 0。
        当队列非空的时候：
            在输出结果 levels 中插入一个空列表，开始当前层的算法。
            计算当前层有多少个元素：等于队列的长度。
            将这些元素从队列中弹出，并加入 levels 当前层的空列表中。
            将他们的孩子节点作为下一层压入队列中。
            进入下一层 level++。

```
from collections import deque
class Solution:

    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        if root is None:
            return 
        res=[]
        queue=deque() # queue=[]
        queue.append(root)
        while len(queue)>0:
            p=queue.popleft() # p=queue.pop(0)
            res.append(p.val)

            if p.left:
                queue.append(p.left)
            if p.right:
                queue.append(p.right)
        
        return res
            
预期输出：[[3],[9,20],[15,7]]
实际输出：[3, 9, 20, 15, 7]

所以，要想得到预期输出，需要增加深度。
```

```
class Solution:

    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        if root is None:
            return 
        res=[]
        queue=[root] # 不能使用这个queue.append([root])
        depth=0
        
        while queue:
            res.append([])
            for i in range(len(queue)):
                node=queue.pop(0)
                res[depth].append(node.val)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            depth+=1
        
        return res
```

```
class Solution:
    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        if not root: return []
        res = []
        cur_level = [root]
        while cur_level:
            tmp = []
            next_level = []
            for node in cur_level:
                tmp.append(node.val)
                if node.left:
                    next_level.append(node.left)
                if node.right:
                    next_level.append(node.right)
            res.append(tmp)
            cur_level = next_level
        return res
```

### 103.二叉树的锯齿形层次遍历
    链接：https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/

    给定一个二叉树，返回其节点值的锯齿形层次遍历。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）。

    例如：
    给定二叉树 [3,9,20,null,null,15,7],

        3
       / \
      9  20
        /  \
       15   7
    返回锯齿形层次遍历如下：

    [
      [3],
      [20,9],
      [15,7]
    ]

思路：锯齿形遍历和层次遍历的差异，如果仍然按层次遍历，但是我要第一层从左往右，第二层从右往左，第三从左往右，这种zigzag遍历方式如何实现？

    对层数进行一个奇偶判断即可，如下：
    if depth %2 == 0:
        res[depth].append(root.val)
    else:
        res[depth].insert(0, root.val)

题解一|递归:
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def zigzagLevelOrder(self, root: TreeNode) -> List[List[int]]:
        res=[]
        def helper(root,depth):
            if not root:
                return
            if len(res)==depth:
                res.append([])
            if depth %2 == 0:
                res[depth].append(root.val)
            else:
                res[depth].insert(0, root.val)
                
            helper(root.left,depth+1)
            helper(root.right,depth+1)
            
        helper(root,0)
        return res
```

题解二|迭代：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def zigzagLevelOrder(self, root: TreeNode) -> List[List[int]]:
        if not root:
            return []
        res=[]
        queue=[root]
        depth=0

        while queue:
            res.append([])
            for i in range(len(queue)):
                node=queue.pop(0)
                if depth % 2 == 0:
                    res[depth].append(node.val)
                else:
                    res[depth].insert(0,node.val)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            depth+=1
        return res
```
### 104. 二叉树的最大深度
    链接：https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/

    给定一个二叉树，找出其最大深度。

    二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

    说明: 叶子节点是指没有子节点的节点。

    示例：
    给定二叉树 [3,9,20,null,null,15,7]，

        3
       / \
      9  20
        /  \
       15   7
    返回它的最大深度 3 。

题解一|递归：
![tree1](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/youdao/tree1.png)
![tree2](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/youdao/tree2.png)


时间复杂度：我们每个结点只访问一次，因此时间复杂度为 O(N)，
其中 N 是结点的数量。

空间复杂度：在最糟糕的情况下，树是完全不平衡的，例如每个结点只剩下左子结点，递归将会被调用 N 次（树的高度），因此保持调用栈的存储将是 O(N)。但在最好的情况下（树是完全平衡的），树的高度将是 log(N)。因此，在这种情况下的空间复杂度将是 O(log(N))。

```
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        if root is None:
            return 0
        else:
            leftHeight=self.maxDepth(root.left)
            rightHeight=self.maxDepth(root.right)
            return max(leftHeight,rightHeight)+1
```

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        if not root:
            return 0
        return max(self.maxDepth(root.left),self.maxDepth(root.right))+1
```

题解二|迭代：

```
class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        if root is None:
            return 0
        stack=[(1,root)]
        depth=0

        while stack:
            curr,root=stack.pop()
            if root: # 如果弹出的节点不为空
                depth=max(depth,curr)
                stack.append((curr+1,root.left))
                stack.append((curr+1,root.right))
        return depth
```

### 105.从前序与中序遍历序列构造二叉树
    链接：https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/

    根据一棵树的前序遍历与中序遍历构造二叉树。

    注意:
    你可以假设树中没有重复的元素。

    例如，给出

    前序遍历 preorder = [3,9,20,15,7]
    中序遍历 inorder = [9,3,15,20,7]
    返回如下的二叉树：

        3
       / \
      9  20
        /  \
       15   7
题解一|递归、模拟法：
    前序遍历和中序遍历有以下特点：
        前序遍历： 根节点 | 左子树 | 右子树 ； 例如：[1 | 2 4 5 | 3 6 7]
        中序遍历： 左子树 | 根节点 | 右子树 ； 例如：[4 2 5 | 1 | 6 3 7]
        对于每个左子树、右子树的前序遍历和中序遍历依然有此规律。
    思路：
        按前序遍历的顺序每次pop并建立节点root，在中序遍历中找到root的对应index，划分出哪些节点构成此节点的左子树inorder[:i]，哪些构成右子树inorder[i+1:]。
        返回值：递归构建完当前节点root左右子树后，返回root，作为上轮递归父节点的left或right。
        终止条件： 当inorder[:i]中序遍历无剩余元素时，说明当前root已经越过叶子节点，直接返回None。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> TreeNode:
        if not inorder or not preorder:
        # if not inorder: # 只能是判断inorder，而不能是preorder，原因应该是preorder.pop(0)操作造成的。
            return
        root=TreeNode(preorder.pop(0))
        i=inorder.index(root.val)
        # print(i,inorder[:i],inorder[i+1:])

        root.left=self.buildTree(preorder,inorder[:i])
        root.right=self.buildTree(preorder,inorder[i+1:])
        return root
```

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> TreeNode:
        if not inorder:
        # if not preorder:
            return
        # 前序遍历第一个值为根节点
        root=TreeNode(preorder[0])
        # 因为没有重复元素，所以可以直接根据值来查找根节点在中序遍历中的位置
        i=inorder.index(preorder[0])
        # print(i,inorder[:i],inorder[i+1:])

        # 构建左子树
        root.left=self.buildTree(preorder[1:i+1],inorder[:i])
        # 构建右子树
        root.right=self.buildTree(preorder[i+1:],inorder[i+1:])
        return root
```

### 106.从中序和后序遍历序列构造二叉树
    链接：https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/

    根据一棵树的中序遍历与后序遍历构造二叉树。

    注意:
    你可以假设树中没有重复的元素。

    例如，给出

    中序遍历 inorder = [9,3,15,20,7]
    后序遍历 postorder = [9,15,7,20,3]
    返回如下的二叉树：

        3
       / \
      9  20
        /  \
       15   7

题解一|递归：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def buildTree(self, inorder: List[int], postorder: List[int]) -> TreeNode: 
        if not inorder:
            return
        root=TreeNode(postorder.pop(-1))
        i=inorder.index(root.val)
        root.right=self.buildTree(inorder[i+1:],postorder) # 注意：需要先右节点，再左节点
        root.left=self.buildTree(inorder[0:i],postorder)
        return root
```

```
class Solution:
    def buildTree(self, inorder: List[int], postorder: List[int]) -> TreeNode: 
        if len(inorder) == 0:
        #if not inorder or not postorder:
            return None
        
        # 后序遍历最后一个节点为根节点
        root = TreeNode(postorder[-1])
        
        # 根节点在中序遍历中的位置
        index = inorder.index(postorder[-1])
        
        # 构造左子树
        root.left = self.buildTree(inorder[:index], postorder[:index])
        
        # 构造右子树
        root.right = self.buildTree(inorder[index+1:], postorder[index:len(postorder) - 1])
        # root.right = self.buildTree(inorder[index+1:], postorder[index:-1])
        
        return root
```
### 107.二叉树的层次遍历 II
    链接：https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/

    给定一个二叉树，返回其节点值自底向上的层次遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

    例如：
    给定二叉树 [3,9,20,null,null,15,7],

        3
       / \
      9  20
        /  \
       15   7
    返回其自底向上的层次遍历为：

    [
      [15,7],
      [9,20],
      [3]
    ]

题解一|递归：
```
class Solution:
    def levelOrderBottom(self, root: TreeNode) -> List[List[int]]:
        res=[]
        def helper(node,depth):
            if not node:
                return
            if len(res)==depth:
                res.insert(0,[])
            
            res[-(depth+1)].append(node.val)
            helper(node.left,depth+1)
            helper(node.right,depth+1)
        helper(root,0)
        return res
```
题解二|迭代：
```
class Solution:
    def levelOrderBottom(self, root: TreeNode) -> List[List[int]]:
        from collections import deque
        if root is None: return
        queue=deque()
        queue.appendleft(root)
        res=[]
        while queue:
            tmp=[]
            for i in range(len(queue)):
                node=queue.pop()
                tmp.append(node.val)
                if node.left:
                    queue.appendleft(node.left)
                if node.right:
                    queue.appendleft(node.right)
            res.insert(0,tmp) # 注意与从上到下层次遍历区别
        return res
```

```
class Solution:
    def levelOrderBottom(self, root: TreeNode) -> List[List[int]]:
        from collections import deque
        if root is None: return
        queue=deque()
        queue.append(root)
        res=[]
        while queue:
            tmp=[]
            for i in range(len(queue)):
                node=queue.popleft()
                tmp.append(node.val)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            res.insert(0,tmp)
        return res
```
### 108.将有序数组转换为二叉搜索树
    链接：https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/
    
    将一个按照升序排列的有序数组，转换为一棵高度平衡二叉搜索树。

    本题中，一个高度平衡二叉树是指一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1。

    示例:

    给定有序数组: [-10,-3,0,5,9],

    一个可能的答案是：[0,-3,9,-10,null,5]，它可以表示下面这个高度平衡二叉搜索树：

          0
         / \
       -3   9
       /   /
     -10  5

二叉搜索树：
    若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值
    若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值
    任意节点的左、右子树也分别为二叉搜索树
    没有键值相等的节点

二叉搜索树的特性：二叉搜索树的中序遍历结果为递增序列。

平衡二叉树，既然要做到平衡，我们只要把根节点选为数组的中点即可。

参考文章：https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/solution/jiang-you-xu-shu-zu-zhuan-huan-wei-er-cha-sou-s-15/

题解一|递归|二分：

1、函数作用
通过上述解题过程我们可以明确该问题的子问题是：构造树的每个节点以及该节点的左右子树。因此，递归函数的作用很明显：

    选取要构造关系的节点并创建它
    构造该节点的左子树
    构造该节点的右子树
函数的输入为递增数组，函数的返回为完成构造的节点。

2、何时结束
当输入的递增数组为空时，只能构成一棵空树，此时返回空节点。

3、何时调用
当构造节点的左右子树时，对递增数组进行拆分并进行递归调用。

时间复杂度：O(n)
空间复杂度：O(log(n))

```
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> TreeNode:
        if not nums: # 此处使用if nums is None会越界。
            return None
        mid=len(nums)//2
        node=TreeNode(nums[mid])

        left=nums[:mid]
        right=nums[mid+1:]

        node.left=self.sortedArrayToBST(left)
        node.right=self.sortedArrayToBST(right)

        # node.left, node.right = map(self.sortedArrayToBST, [nums[:mid], nums[mid+1:]])

        return node
```

扩展：将有序数组转换为二叉搜索树的结果为什么 不唯一 ？

众所周知，二叉搜索树的中序遍历是一个升序序列。

将有序数组作为输入，可以把该问题看做根据中序遍历序列创建二叉搜索树。

    这个问题的答案唯一吗。例如：是否可以根据中序遍历序列和二叉搜索树之间是否一一对应，答案是否定的。

关于 BST 的知识：

    中序遍历不能唯一确定一棵二叉搜索树。
    先序和后序遍历不能唯一确定一棵二叉搜索树。
    先序/后序遍历和中序遍历的关系：
    inorder = sorted(postorder) = sorted(preorder)，
    中序+后序、中序+先序可以唯一确定一棵二叉树。

因此，“有序数组 -> BST”有多种答案：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/bitTree1.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/bitTree1.png)

因此，添加一个附件条件：树的高度应该是平衡的、例如：每个节点的两棵子树高度差不超过 1。

    这种情况下答案唯一吗？仍然没有。

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/bitTree2.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/bitTree2.png)

高度平衡意味着每次必须选择中间数字作为根节点。这对于奇数个数的数组是有用的，但对偶数个数的数组没有预定义的选择方案。

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/bitTree3.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/bitTree3.png)

对于偶数个数的数组，要么选择中间位置左边的元素作为根节点，要么选择中间位置右边的元素作为根节点，不同的选择方案会创建不同的平衡二叉搜索树。方法一始终选择中间位置左边的元素作为根节点，方法二始终选择中间位置右边的元素作为根节点。方法一和二会生成不同的二叉搜索树，这两种答案都是正确的。

#### 1、中序遍历：始终选择中间位置左边元素作为根节点

时间复杂度：O(N)，每个元素只访问一次。
空间复杂度：O(N)，二叉搜索树空间 O(N)，递归栈深度 O(logN)。

```
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> TreeNode:        
        def helper(left, right):
            if left > right:
                return None

            mid=(left+right)//2
            root=TreeNode(nums[mid])
            root.left=helper(left,mid-1)
            root.right=helper(mid+1,right)
            return root

        return helper(0,len(nums)-1)
```
#### 2、中序遍历：始终选择中间位置右边元素作为根节点

时间复杂度：O(N)，每个元素只访问一次。
空间复杂度：O(N)，二叉搜索树空间 O(N)，递归栈深度 O(logN)。

```
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> TreeNode:        
        def helper(left, right):
            if left > right:
                return None

            mid=(left+right)//2
            if (left+right) % 2:
                mid+=1
            root=TreeNode(nums[mid])
            root.left=helper(left,mid-1)
            root.right=helper(mid+1,right)
            return root

        return helper(0,len(nums)-1)

```
#### 3、中序遍历：选择任意一个中间位置元素作为根节点

不做预定义选择，每次随机选择中间位置左边或者右边元素作为根节点。每次运行的结果都不同，但都是正确的。

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/bitTree4.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/bitTree4.png)

时间复杂度：O(N)，每个元素只访问一次。
空间复杂度：O(N)，二叉搜索树空间 O(N)，递归栈深度 O(logN)。

```
from random import randint

class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> TreeNode:        
        def helper(left, right):
            if left > right:
                return None

            mid=(left+right)//2
            if (left+right) % 2:
                mid+=randint(0,1)
            root=TreeNode(nums[mid])
            root.left=helper(left,mid-1)
            root.right=helper(mid+1,right)
            return root

        return helper(0,len(nums)-1)

```

扩展：关于Python not 及is None的有趣现象（两者的区别）

    在使用 if A is None 来实现判断条件的时候，最后程序没有通过。这里的A代表之前定义的list类型的数据。最用改成if not A才通过。

    那么到底判断A为空两者有什么区别呢。

    发现A为空时，not A 就是True，但是这并不代表该对象没有定义，也不代表该对象没有其它的属性。它只是代表A中元素为空，仅此而已。

    如果要看对象是否有定义，就要使用 is None来判断。

    if not nums 和 if nums is None 有什么区别？
    空列表, 0, None都满足第一个条件, 但是第二个只有None才满足

### 110.平衡二叉树
    链接：https://leetcode-cn.com/problems/balanced-binary-tree/

    给定一个二叉树，判断它是否是高度平衡的二叉树。

    本题中，一棵高度平衡二叉树定义为：

    一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过1。

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
    返回 false 。


题解一|递归|自顶向下：

构造一个获取当前节点最大深度的方法 depth() ，通过比较左右子树最大高度差abs(self.depth(root.left) - self.depth(root.right))，来判断以此节点为根节点下是否是二叉平衡树；

从顶至底DFS，以每个节点为根节点，递归判断是否是平衡二叉树：

    若所有根节点都满足平衡二叉树性质，则返回 True ；
    若其中任何一个节点作为根节点时，不满足平衡二叉树性质，则返回False。

本方法产生大量重复的节点访问和计算，最差情况下时间复杂度 O(N^2)。

```
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class Solution:
    def isBalanced(self, root: TreeNode) -> bool:
        if root is None:
            return True
        return abs(self.height(root.left)-self.height(root.right))<=1 and self.isBalanced(root.left) and self.isBalanced(root.right)
           
    def height(self,node):
        if not node:
            return 0
        return max(self.height(node.left),self.height(node.right))+1

```
?题解二|自底向上：

对二叉树做深度优先遍历DFS，递归过程中：

    终止条件：当DFS越过叶子节点时，返回高度0；
    返回值：
        从底至顶，返回以每个节点root为根节点的子树最大高度(左右子树中最大的高度值加1，max(left,right) + 1)；
        当我们发现有一例 左/右子树高度差 ＞ 1 的情况时，代表此树不是平衡树，返回-1；

    当发现不是平衡树时，后面的高度计算都没有意义了，因此一路返回-1，避免后续多余计算。

最差情况是对树做一遍完整DFS，时间复杂度为 O(N)。

```
class Solution:
    def isBalanced(self, root: TreeNode) -> bool:
        return self.depth(root) != -1
    def depth(self,root):
        if not root:
            return 0
        left=self.depth(root.left)
        if left == -1:
            return -1
        right=self.depth(root.right)
        if right == -1:
            return -1
        
        return max(left,right)+1 if abs(left-right)<=1 else -1
```

### 111.二叉树的最小深度
    链接：https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/

    给定一个二叉树，找出其最小深度。

    最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

    说明: 叶子节点是指没有子节点的节点。

    示例:

    给定二叉树 [3,9,20,null,null,15,7],

        3
       / \
      9  20
        /  \
       15   7
    返回它的最小深度  2.

题解一|递归：
```
class Solution:
    def minDepth(self, root: TreeNode) -> int:
        if root is None:
            return 0
        else:
            leftHeight=self.minDepth(root.left)
            rightHeight=self.minDepth(root.right)
            if not root.left:
                return rightHeight+1
            if not root.right:
                return leftHeight+1
            return min(leftHeight,rightHeight)+1
```

思路：

    1. 当 root 节点左右孩子都为空时，返回 1
    2. 当 root 节点左右孩子有一个为空时，返回不为空的孩子节点的深度
    3. 当 root 节点左右孩子都不为空时，返回左右孩子较小深度的节点值
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def minDepth(self, root: TreeNode) -> int:
        if not root:
            return 0
        left=self.minDepth(root.left)
        right=self.minDepth(root.right)
        if not root.left and not root.right:
            return 1
        elif not root.left or not root.right:
            if root.left:
                return left+1
            else:
                return right+1
        else:
            return min(left,right)+1
```

题解二|层次遍历:
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def minDepth(self, root: TreeNode) -> int:
        from collections import deque
        if root is None:
            return 0
        queue=deque()
        queue.append((root,1)) # 不能使用queue=deque((root,1))
        while queue:
            node,dep=queue.popleft()
            if not node.left and not node.right:
                return dep
            if node.left:
                queue.append((node.left,dep+1))
            if node.right:
                queue.append((node.right,dep+1))
```

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

### 114. 二叉树展开为链表
    链接：https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/

    给定一个二叉树，原地将它展开为一个单链表。

    例如，给定二叉树

        1
       / \
      2   5
     / \   \
    3   4   6
    将其展开为：

    1
     \
      2
       \
        3
         \
          4
           \
            5
             \
              6

思路：

    将左子树插入到右子树的地方；
    将原来的右子树接到左子树的最右边节点；
    考虑新的右子树的根节点，一直重复上边的过程，直到新的右子树为 null；

        1
       / \
      2   5
     / \   \
    3   4   6

    //将 1 的左子树插入到右子树的地方
        1
         \
          2         5
         / \         \
        3   4         6        
    //将原来的右子树接到左子树的最右边节点
        1
         \
          2          
         / \          
        3   4  
             \
              5
               \
                6
                
    //将 2 的左子树插入到右子树的地方
        1
         \
          2          
           \          
            3       4  
                     \
                      5
                       \
                        6   
            
    //将原来的右子树接到左子树的最右边节点
        1
         \
          2          
           \          
            3      
             \
              4  
               \
                5
                 \
                  6         

题解一|递归：
```
class Solution:
    def flatten(self, root: TreeNode) -> None:
        """
        Do not return anything, modify root in-place instead.
        """
        if not root:
            return 
        if root.left: 
            pre=root.left # pre指向左子树
            while pre.right:
                pre=pre.right # 找到左子树的最右节点
            pre.right=root.right # 左子树的最右节点的右子树指向根节点的右子树
            root.right=root.left # 令根节点的右子树指向根节点的左子树
            root.left=None # 置空根节点的左子树
        self.flatten(root.left)
        self.flatten(root.right)
```

题解二|迭代：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def flatten(self, root: TreeNode) -> None:
        """
        Do not return anything, modify root in-place instead.
        """
        while root:
            if root.left:
                tmp=root.left
                while tmp.right:
                    tmp=tmp.right
                tmp.right=root.right
                root.right=root.left
                root.left=None
            root=root.right
        return 
```

### 116. 填充每个节点的下一个右侧节点指针
    链接：https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/

    给定一个完美二叉树，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：

    struct Node {
      int val;
      Node *left;
      Node *right;
      Node *next;
    }
    填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL。

    初始状态下，所有 next 指针都被设置为 NULL。

    示例：

    输入：{"$id":"1","left":{"$id":"2","left":{"$id":"3","left":null,"next":null,"right":null,"val":4},"next":null,"right":{"$id":"4","left":null,"next":null,"right":null,"val":5},"val":2},"next":null,"right":{"$id":"5","left":{"$id":"6","left":null,"next":null,"right":null,"val":6},"next":null,"right":{"$id":"7","left":null,"next":null,"right":null,"val":7},"val":3},"val":1}

    输出：{"$id":"1","left":{"$id":"2","left":{"$id":"3","left":null,"next":{"$id":"4","left":null,"next":{"$id":"5","left":null,"next":{"$id":"6","left":null,"next":null,"right":null,"val":7},"right":null,"val":6},"right":null,"val":5},"right":null,"val":4},"next":{"$id":"7","left":{"$ref":"5"},"next":null,"right":{"$ref":"6"},"val":3},"right":{"$ref":"4"},"val":2},"next":null,"right":{"$ref":"7"},"val":1}

    解释：给定二叉树如图 A 所示，你的函数应该填充它的每个 next 指针，以指向其下一个右侧节点，如图 B 所示。
     
    提示：

    你只能使用常量级额外空间。
    使用递归解题也符合要求，本题中递归程序占用的栈空间不算做额外的空间复杂度。

题解一|递归：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, val: int = 0, left: 'Node' = None, right: 'Node' = None, next: 'Node' = None):
        self.val = val
        self.left = left
        self.right = right
        self.next = next
"""
class Solution:
    def connect(self, root: 'Node') -> 'Node':
        def helper(root):
            if not root:
                return
            l=root.left
            r=root.right
            while l: # while l and r:也可以
                l.next=r
                l=l.right
                r=r.left
            helper(root.left)
            helper(root.right)
        helper(root)
        return root
```

题解二|迭代：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, val: int = 0, left: 'Node' = None, right: 'Node' = None, next: 'Node' = None):
        self.val = val
        self.left = left
        self.right = right
        self.next = next
"""
class Solution:
    def connect(self, root: 'Node') -> 'Node':
        if not root:
            return
        queue=[root]
        while queue:
            size=len(queue)
            for i in range(size):
                tmp=queue.pop(0)
                if i < size-1:
                    tmp.next=queue[0]
                if tmp.left:
                    queue.append(tmp.left)
                if tmp.right:
                    queue.append(tmp.right)
        return root
```

### 117. 填充每个节点的下一个右侧节点指针 II

    链接：https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/

    给定一个二叉树

    struct Node {
      int val;
      Node *left;
      Node *right;
      Node *next;
    }
    填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL。

    初始状态下，所有 next 指针都被设置为 NULL。

    进阶：

    你只能使用常量级额外空间。
    使用递归解题也符合要求，本题中递归程序占用的栈空间不算做额外的空间复杂度。
     
    示例：

    输入：root = [1,2,3,4,5,null,7]
    输出：[1,#,2,3,#,4,5,7,#]
    解释：给定二叉树如图 A 所示，你的函数应该填充它的每个 next 指针，以指向其下一个右侧节点，如图 B 所示。
     

    提示：

    树中的节点数小于 6000
    -100 <= node.val <= 100

？？题解一|递归：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, val: int = 0, left: 'Node' = None, right: 'Node' = None, next: 'Node' = None):
        self.val = val
        self.left = left
        self.right = right
        self.next = next
"""
class Solution:
    def connect(self, root: 'Node') -> 'Node':
        def helper(root):
            if not root:
                return
            if root.left:
                return root.left
            if root.right:
                return root.right
            if root.next:
                return helper(root.next)
            return

        if not root:
            return
        if root.left and root.right:
            root.left.next=root.right
            root.right.next=helper(root.next)
        elif root.left:
            root.left.next=helper(root.next)
        elif root.right:
            root.right.next=helper(root.next)

        # 注意：先递归右子树，否则右子树根节点next关系没建立好，左子树到右子树子节点无法正确挂载
        self.connect(root.right)
        self.connect(root.left)
        return root
```

题解二|迭代：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, val: int = 0, left: 'Node' = None, right: 'Node' = None, next: 'Node' = None):
        self.val = val
        self.left = left
        self.right = right
        self.next = next
"""

class Solution:
    def connect(self, root: 'Node') -> 'Node':
        if not root:
            return
        queue=[root]
        while queue:
            size=len(queue)
            for i in range(size):
                tmp=queue.pop(0)
                if i < size-1:
                    tmp.next=queue[0]
                if tmp.left:
                    queue.append(tmp.left)
                if tmp.right:
                    queue.append(tmp.right)
        return root
```

### 124. 二叉树中的最大路径和
    链接：https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/

    给定一个非空二叉树，返回其最大路径和。

    本题中，路径被定义为一条从树中任意节点出发，达到任意节点的序列。该路径至少包含一个节点，且不一定经过根节点。

    示例 1:

    输入: [1,2,3]

           1
          / \
         2   3

    输出: 6
    示例 2:

    输入: [-10,9,20,null,null,15,7]

       -10
       / \
      9  20
        /  \
       15   7

    输出: 42

??题解一|递归：

参考：https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/solution/shou-hui-tu-jie-hen-you-ya-de-yi-dao-dfsti-by-hyj8/


https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/solution/er-cha-shu-de-zui-da-lu-jing-he-zhu-yao-li-jie-ti-/

关键就是区分：

1. 当前节点最大路径和计算：以当前节点为起点的所有路径和
2. 当前节点对上一层的贡献：只能选择当前节点的最大的一条路径作为贡献，因为路径节点不可重复

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def maxPathSum(self, root: TreeNode) -> int:
        self.ans=float('-inf')
        def helper(root):
            if not root:
                return 0
            # 递归计算左右子节点的最大贡献值
            # 只有在最大贡献值大于0时，才回选取对应子节点
            leftMax=max(0,helper(root.left)) # 左孩子贡献
            rightMax=max(0,helper(root.right)) # 右孩子贡献

            # 节点的最大路径和取决于该节点值与该节点的左右节点的最大贡献值
            self.ans=max(self.ans,root.val+leftMax+rightMax) # 更新答案

            # 返回节点的最大贡献值
            return root.val+max(leftMax,rightMax)
        helper(root)
        return self.ans
```


### 129. 求根到叶子节点数字之和
    链接：https://leetcode-cn.com/problems/sum-root-to-leaf-numbers/

    给定一个二叉树，它的每个结点都存放一个 0-9 的数字，每条从根到叶子节点的路径都代表一个数字。

    例如，从根到叶子节点路径 1->2->3 代表数字 123。

    计算从根到叶子节点生成的所有数字之和。

    说明: 叶子节点是指没有子节点的节点。

    示例 1:

    输入: [1,2,3]
        1
       / \
      2   3
    输出: 25
    解释:
    从根到叶子节点路径 1->2 代表数字 12.
    从根到叶子节点路径 1->3 代表数字 13.
    因此，数字总和 = 12 + 13 = 25.
    示例 2:

    输入: [4,9,0,5,1]
        4
       / \
      9   0
     / \
    5   1
    输出: 1026
    解释:
    从根到叶子节点路径 4->9->5 代表数字 495.
    从根到叶子节点路径 4->9->1 代表数字 491.
    从根到叶子节点路径 4->0 代表数字 40.
    因此，数字总和 = 495 + 491 + 40 = 1026.

题解一|递归：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def sumNumbers(self, root: TreeNode) -> int:
        res=[]
        def helper(root,tmp=''):
            if not root:
                return 
            if not root.left and not root.right:
                tmp+=str(root.val)
                res.append(tmp)
            helper(root.left,tmp+str(root.val))
            helper(root.right,tmp+str(root.val))
        helper(root)
        ans=0
        for i in res:
            ans+=int(i)
        return ans
```

### 144.二叉树的前序遍历
    链接：https://leetcode-cn.com/problems/binary-tree-preorder-traversal/

    给定一个二叉树，返回它的 前序 遍历。

     示例:

    输入: [1,null,2,3]  
       1
        \
         2
        /
       3 

    输出: [1,2,3]
    进阶: 递归算法很简单，你可以通过迭代算法完成吗？

题解一|递归：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def preorderTraversal(self, root: TreeNode) -> List[int]:
        ans=[]
        if root:
            ans.append(root.val)
            ans+=self.preorderTraversal(root.left) # 这儿我之前直接用append，出来的结果会列表中嵌套列表
            ans+=self.preorderTraversal(root.right)
        return ans
```

题解二|迭代|辅助栈|后进先出：

1、把二叉树的根节点 root 放进栈。

2、如果栈不为空，从栈中取出一个节点，把该节点放入容器的尾部；如果该节点的右子树不为空，则把有节点放入栈；如果该节点的左子树不为空，则把左子树放入栈中。

3、一直重复步骤 2 ，直到栈为空，此时遍历结束。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def preorderTraversal(self, root: TreeNode) -> List[int]:
        res=[]
        stack=[]
        stack.append(root)
        if root is None:
            return res
        while stack:
            tmp=stack.pop()
            res.append(tmp.val)
            if tmp.right:
                stack.append(tmp.right)
            if tmp.left:
                stack.append(tmp.left)
        return res
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
    def preorderTraversal(self, root: TreeNode) -> List[int]:
        res=[]
        stack=[]
        curr=root
        while stack or curr:
            if curr:
                res.append(curr.val)
                stack.append(curr.right)
                curr=curr.left
            else:
                curr=stack.pop()
        return res
```
### 145.二叉树的后序遍历
    链接：

    给定一个二叉树，返回它的 后序 遍历。

    示例:

    输入: [1,null,2,3]  
       1
        \
         2
        /
       3 

    输出: [3,2,1]
    进阶: 递归算法很简单，你可以通过迭代算法完成吗？

题解一|递归:
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def postorderTraversal(self, root: TreeNode) -> List[int]:
        res=[]
        if root:
            res=self.postorderTraversal(root.left)
            res+=self.postorderTraversal(root.right)
            res.append(root.val)
        return res
```

题解二|迭代|辅助栈：

1、把二叉树的根节点 root 放进栈。

2、如果栈不为空，从栈中取出一个节点，把该节点插入到容器的头部。；如果该节点的左子树不为空，则把该左子树放入栈中；如果该节点的右子树不为空，则把右子树放入栈中。，

注意，之前的前序遍历和中序遍历，我们都是用 ArrayList 容器，并且是把节点插入到容器的尾部，这就是后序遍历的不同点。

3、一直重复步骤 2 ，直到栈为空，此时遍历结束。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def postorderTraversal(self, root: TreeNode) -> List[int]:
        res=[]
        stack=[]
        if root is None:
            return res
        stack.append(root)
        while stack:
            tmp=stack.pop()
            res.insert(0,tmp.val)
            if tmp.left:
                stack.append(tmp.left)
            if tmp.right:
                stack.append(tmp.right)
        return res
```

题解三|递归：

参考：https://leetcode-cn.com/problems/binary-tree-postorder-traversal/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--34/

如果当前节点的右节点和上一次遍历的节点相同，那就表明当前是从右节点过来的了。
```
class Solution:
    def postorderTraversal(self, root: TreeNode) -> List[int]:
        res=[]
        stack=[]
        cur=root
        last=None
        
        while cur or stack:
            if cur:
                stack.append(cur)
                cur=cur.left
            else:
                tmp=stack[-1]
                # 是否变到右子树
                if tmp.right and tmp.right !=last:
                    cur=tmp.right
                else:
                    res.append(tmp.val)
                    last=tmp
                    stack.pop()
        return res

```


### 173. 二叉搜索树迭代器
    链接：https://leetcode-cn.com/problems/binary-search-tree-iterator/

    实现一个二叉搜索树迭代器。你将使用二叉搜索树的根节点初始化迭代器。

    调用 next() 将返回二叉搜索树中的下一个最小的数。

    示例：

    BSTIterator iterator = new BSTIterator(root);
    iterator.next();    // 返回 3
    iterator.next();    // 返回 7
    iterator.hasNext(); // 返回 true
    iterator.next();    // 返回 9
    iterator.hasNext(); // 返回 true
    iterator.next();    // 返回 15
    iterator.hasNext(); // 返回 true
    iterator.next();    // 返回 20
    iterator.hasNext(); // 返回 false
     

    提示：

    next() 和 hasNext() 操作的时间复杂度是 O(1)，并使用 O(h) 内存，其中 h 是树的高度。
    你可以假设 next() 调用总是有效的，也就是说，当调用 next() 时，BST 中至少存在一个下一个最小的数。

题解一|扁平化二叉搜索树：

时间复杂度：

构造迭代器花费的时间为 O(N)，问题陈述只要求我们分析两个函数的复杂性，但是在实现类时，还要注意初始化类对象所需的时间；在这种情况下，时间复杂度与二叉搜索树中的节点数成线性关系。

    next()：O(1)
    hasNext()：O(1)

空间复杂度：

O(N)，由于我们创建了一个数组来包含二叉搜索树中的所有节点值，这不符合问题陈述中的要求，任一函数的最大空间复杂度应为 O(h)，其中 h 指的是树的高度，对于平衡的二叉搜索树，高度通常为 logN。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class BSTIterator:

    def __init__(self, root: TreeNode):
        self.queue=[]
        self.index=-1
        self.inorder(root)
    
    def inorder(self,root):
        if not root:
            return
        self.inorder(root.left)
        self.queue+=[root.val] # self.queue.append([root.val])
        self.inorder(root.right)

    def next(self) -> int:
        """
        @return the next smallest number
        """
        self.index+=1
        return self.queue[self.index]

    def hasNext(self) -> bool:
        """
        @return whether we have a next smallest number
        """
        return self.index+1 < len(self.queue)

# Your BSTIterator object will be instantiated and called as such:
# obj = BSTIterator(root)
# param_1 = obj.next()
# param_2 = obj.hasNext()
```

题解二|受控递归：

时间复杂度：

    hasNext()：若栈中还有元素，则返回 true，反之返回 false。所以这是一个 O(1) 的操作。

    next()：包含了两个主要步骤。一个是从栈中弹出一个元素，它是下一个最小的元素。这是一个 O(1) 的操作。然而，随后我们要调用帮助函数 _inorder_left ，它需要递归的，将左节点添加到栈上，是线性时间的操作，最坏的情况下为 O(N)。但是我们只对含有右节点的节点进行调用，它也不会总是处理 N 个节点。只有当我们有一个倾斜的树，才会有 N 个节点。因此该操作的平均时间复杂度仍然是 O(1)，符合问题中所要求的。

空间复杂度：O(h)，使用了一个栈来模拟递归。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class BSTIterator:

    def __init__(self, root: TreeNode):
        self.queue=[]
        self.inorder(root)
    
    def inorder(self,root):
        while root:
            self.queue.append(root)
            root=root.left

    def next(self) -> int:
        """
        @return the next smallest number
        """
        top=self.queue.pop()
        if top.right:
            self.inorder(top.right)
        return top.val

    def hasNext(self) -> bool:
        """
        @return whether we have a next smallest number
        """
        return len(self.queue) > 0



# Your BSTIterator object will be instantiated and called as such:
# obj = BSTIterator(root)
# param_1 = obj.next()
# param_2 = obj.hasNext()
```

### 199. 二叉树的右视图
    链接：https://leetcode-cn.com/problems/binary-tree-right-side-view/

    给定一棵二叉树，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。

    示例:

    输入: [1,2,3,null,5,null,4]
    输出: [1, 3, 4]
    解释:

       1            <---
     /   \
    2     3         <---
     \     \
      5     4       <---

题解一|递归：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def rightSideView(self, root: TreeNode) -> List[int]:
        res=[]
        def helper(root,level):
            if not root:
                return []
            if level > len(res):
                res.append(root.val)
            helper(root.right,level+1)
            helper(root.left,level+1)
        helper(root,1)
        return res
```

题解二|广度优先搜索：

时间复杂度 : O(n)。 每个节点最多进队列一次，出队列一次，因此广度优先搜索的复杂度为线性。

空间复杂度 : O(n)。每个节点最多进队列一次，所以队列长度最大不不超过 n，所以这里的空间代价为 O(n)。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution(object):
    def rightSideView(self, root):
        right=dict()
        max_depth=-1
        queue=[(root,0)]
        while queue:
            node,depth=queue.pop(0)
            if node:
                max_depth=max(max_depth,depth)
                right[depth]=node.val
                queue.append((node.left,depth+1))
                queue.append((node.right,depth+1))
        return [right[i] for i in range(max_depth+1)]
```

题解三|深度优先搜索：

时间复杂度 : O(n)。深度优先搜索最多访问每个结点一次，因此是线性复杂度。

空间复杂度 : O(n)。最坏情况下，栈内会包含接近树高度的结点数量，占用 O(n) 的空间。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution(object):
    def rightSideView(self, root):
        right=dict()
        max_depth=-1
        queue=[(root,0)]
        while queue:
            node,depth=queue.pop()
            if node:
                max_depth=max(max_depth,depth)
                right.setdefault(depth,node.val) # 和get()类似，setdefault()如果键不存在于字典中，将会添加键并将值设为默认值。
                queue.append((node.left,depth+1))
                queue.append((node.right,depth+1))
        return [right[i] for i in range(max_depth+1)]
```


### 222.完全二叉树的节点个数
    链接：https://leetcode-cn.com/problems/count-complete-tree-nodes/

    给出一个完全二叉树，求出该树的节点个数。

    说明：

    完全二叉树的定义如下：在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。若最底层为第 h 层，则该层包含 1~ 2h 个节点。

    示例:

    输入: 
        1
       / \
      2   3
     / \  /
    4  5 6

    输出: 6

题解一|递归：

时间复杂度：O(n)。
空间复杂度：O(logn)，其中 d 指的是树的的高度，运行过程中堆栈所使用的空间。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def countNodes(self, root: TreeNode) -> int:
        
        self.res=0
        def helper(root):
            if not root:
                return 0
            if root:
                self.res+=1
            helper(root.left)
            helper(root.right)
            return self.res
        helper(root)
        
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
    def __init__(self):
        self.res=0
    def countNodes(self, root: TreeNode) -> int:
        if not root:
            return 0
        self.res+=1
        self.countNodes(root.left)
        self.countNodes(root.right)
        return self.res
```

```
class Solution:
    def countNodes(self, root: TreeNode) -> int:
        return 1 + self.countNodes(root.right) + self.countNodes(root.left) if root else 0
```

### 226. 翻转二叉树
    链接：https://leetcode-cn.com/problems/invert-binary-tree/

    翻转一棵二叉树。

    示例：

    输入：

         4
       /   \
      2     7
     / \   / \
    1   3 6   9
    输出：

         4
       /   \
      7     2
     / \   / \
    9   6 3   1
    备注:
    这个问题是受到 Max Howell 的 原问题 启发的 ：

    谷歌：我们90％的工程师使用您编写的软件(Homebrew)，但是您却无法在面试时在白板上写出翻转二叉树这道题，这太糟糕了。

题解一|递归：

交换一下左右节点，然后再递归的交换左节点，右节点。

递归的两个条件：

    终止条件：当前节点为null时返回
    交换当前节点的左右节点，再递归的交换当前节点的左节点，递归的交换当前节点的右节点

时间复杂度：每个元素都必须访问一次，所以是O(n)
空间复杂度：最坏的情况下，需要存放O(h)个函数调用(h是树的高度)，所以是O(h)

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Solution:
    def invertTree(self, root: TreeNode) -> TreeNode:
        if not root:
            return
        root.left,root.right=self.invertTree(root.right),self.invertTree(root.left)
        return root
```

```
class Solution:
    def invertTree(self, root: TreeNode) -> TreeNode:
        if not root:
            return
        root.left,root.right=root.right,root.left
        self.invertTree(root.left)
        self.invertTree(root.right)
        return root
```

题解二|迭代|广度优先遍历）：

递归实现也就是深度优先遍历的方式，那么对应的就是广度优先遍历。

广度优先遍历需要额外的数据结构--队列，来存放临时遍历到的元素。

深度优先遍历的特点是一竿子插到底，不行了再退回来继续；而广度优先遍历的特点是层层扫荡。
所以，我们需要先将根节点放入到队列中，然后不断的迭代队列中的元素。
对当前元素调换其左右子树的位置，然后：

    判断其左子树是否为空，不为空就放入队列中
    判断其右子树是否为空，不为空就放入队列中

时间复杂度：同样每个节点都需要入队列/出队列一次，所以是O(n)
空间复杂度：最坏的情况下会包含所有的叶子节点，完全二叉树叶子节点是n/2个，所以时间复杂度是0(n)

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def invertTree(self, root: TreeNode) -> TreeNode:
        if not root:
            return
        queue=[root] # 将二叉树中的节点逐层放入队列中，再迭代处理队列中的元素
        while queue:
            tmp=queue.pop(0) # 每次都从队列中拿一个节点，并交换这个节点的左右子树
            tmp.left,tmp.right=tmp.right,tmp.left
            if tmp.left: # 如果当前节点的左子树不为空，则放入队列等待后续处理 
                queue.append(tmp.left)
            if tmp.right: # 如果当前节点的右子树不为空，则放入队列等待后续处理    
                queue.append(tmp.right)
        return root # 返回处理完的根节点
```

### 230. 二叉搜索树中第K小的元素
    给定一个二叉搜索树，编写一个函数 kthSmallest 来查找其中第 k 个最小的元素。

    说明：
    你可以假设 k 总是有效的，1 ≤ k ≤ 二叉搜索树元素个数。

    示例 1:

    输入: root = [3,1,4,null,2], k = 1
       3
      / \
     1   4
      \
       2
    输出: 1
    示例 2:

    输入: root = [5,3,6,2,4,null,null,1], k = 3
           5
          / \
         3   6
        / \
       2   4
      /
     1
    输出: 3
    进阶：
    如果二叉搜索树经常被修改（插入/删除操作）并且你需要频繁地查找第 k 小的值，你将如何优化 kthSmallest 函数？

题解一|递归|中序遍历：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def kthSmallest(self, root: TreeNode, k: int) -> int:
        res=[]
        def inorder(root):
            if not root:
                return []
            return inorder(root.left)+[root.val]+inorder(root.right)
        return inorder(root)[k-1]
```
题解二|迭代：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def kthSmallest(self, root: TreeNode, k: int) -> int:
        stack=[]
        while True:
            while root:
                stack.append(root)
                root=root.left
            root=stack.pop()
            k-=1
            if not k:
                return root.val
            root=root.right
```

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def kthSmallest(self, root: TreeNode, k: int) -> int:
        if not root or not k:
            return
        stack=[]
        while stack or root:
            if root:
                stack.append(root)
                root=root.left
            else:
                root=stack.pop()
                k-=1
                if not k:
                    return root.val
                root=root.right
```


### 235. 二叉搜索树的最近公共祖先
    链接：https://leetcode-cn.com/problems/
    lowest-common-ancestor-of-a-binary-search-tree/

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

题解一|递归：

思路：

    1、从根节点开始遍历树
    2、如果节点 p 和节点 q 都在右子树上，那么以右孩子为根节点继续 1 的操作
    3、如果节点 p 和节点 q 都在左子树上，那么以左孩子为根节点继续 1 的操作
    4、如果条件 2 和条件 3 都不成立，这就意味着我们已经找到节 p 和节点 q 的 最近公共祖先(LCA) 了

时间复杂度：O(N)
其中 N 为 BST 中节点的个数，在最坏的情况下我们可能需要访问 BST 中所有的节点。

空间复杂度：O(N)
所需开辟的额外空间主要是递归栈产生的，之所以是 N 是因为 BST 的高度为 N。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if not root:
            return
        if p.val < root.val and q.val < root.val:
            return self.lowestCommonAncestor(root.left,p,q)
        elif p.val > root.val and q.val > root.val:
            return self.lowestCommonAncestor(root.right,p,q)
        else:
            return root
```

题解二|迭代：

时间复杂度：O(N)
其中 NN 为 BST 中节点的个数，在最坏的情况下我们可能需要遍历 BST 中所有的节点。

空间复杂度：O(1)
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if not root:
            return
        while root:
            if p.val < root.val and q.val < root.val:
                root=root.left
            elif p.val > root.val and q.val > root.val:
                root=root.right
            else:
                return root
```

### 236. 二叉树的最近公共祖先
    链接：https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/

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

题解一|递归：

最近公共祖先的定义： 设节点 root 为节点 p, q 的某公共祖先，若其左子节点 root.left 和右子节点 root.right 都不是 p,q 的公共祖先，则称 root 是 “最近的公共祖先” 。

根据以上定义，若 root 是 p, q 的 最近公共祖先 ，则只可能为以下情况之一：

p 和 q 在 root 的子树中，且分列 root 的 异侧（即分别在左、右子树中）；
p = root ，且 q 在 root 的左或右子树中；
q = root ，且 p 在 root 的左或右子树中；

递归解析：

1、终止条件：

    当越过叶节点，则直接返回 null ；
    当 root 等于 p, q，则直接返回 root ；
2、递推工作：

    开启递归左子节点，返回值记为 left ；
    开启递归右子节点，返回值记为 right ；
3、返回值： 根据 left 和 right ，可展开为四种情况；
    
    (1)当 left 和 right 同时为空 ：说明 root 的左/右子树中都不包含 p,q ，返回 null ；
    (2)当 left 和 right 同时不为空 ：说明 p, q 分列在 root 的 异侧 （分别在 左 / 右子树），因此 root 为最近公共祖先，返回 root ；
    (3)当 left 为空 ，right 不为空 ：p,q 都不在 root 的左子树中，直接返回 right。具体可分为两种情况：

        p,q 其中一个在 root 的 右子树 中，此时 right 指向 p（假设为 p ）；
        p,q 两节点都在 root 的 右子树 中，此时的 right 指向 最近公共祖先节点 ；
    
    (4)当 left 不为空 ， right 为空 ：与情况 3. 同理；

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if not root or root == p or root == q:
            return root
        left=self.lowestCommonAncestor(root.left,p,q)
        right=self.lowestCommonAncestor(root.right,p,q)
        if left and right: # 1
            return root
        elif not left and not right: # 2
            return 
        elif not left:  # 3
            return right
        elif not right: # 4
            return left
```

### 257. 二叉树的所有路径
    链接：https://leetcode-cn.com/problems/binary-tree-paths/

    给定一个二叉树，返回所有从根节点到叶子节点的路径。

    说明: 叶子节点是指没有子节点的节点。
    示例:

    输入:

       1
     /   \
    2     3
     \
      5

    输出: ["1->2->5", "1->3"]

    解释: 所有根节点到叶子节点的路径为: 1->2->5, 1->3

https://www.dazhuanlan.com/2019/10/18/5da9456f33ee7/

题解一|递归：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def binaryTreePaths(self, root: TreeNode) -> List[str]:
        if not root:
            return []
        res=[]
        def helper(root,path):
            if not root.left and not root.right:
                res.append(path)
            if root.left:
                helper(root.left,path+'->'+str(root.left.val))
            if root.right:
                helper(root.right,path+'->'+str(root.right.val))
        helper(root,''+str(root.val))
        return res
```

题解二|回溯：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def binaryTreePaths(self, root: TreeNode) -> List[str]:
        res=[]
        def helper(root,path):
            if root:
                path.append(str(root.val))
                left=helper(root.left,path)
                right=helper(root.right,path)
                if not left and not right:
                    res.append('->'.join(path))
                path.pop()
                return True  # 别漏掉，用来判断left和right的True和False

        helper(root,[])
        return res
```

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def binaryTreePaths(self, root: TreeNode) -> List[str]:
        if not root:
            return []
        res=[]
        def helper(node,path):
            path.append(str(node.val))
            if not node.left and not node.right:
                res.append('->'.join(path))
                return 
            if node.left:
                helper(node.left,path)
                path.pop()
            if node.right:
                helper(node.right,path)
                path.pop()
        helper(root,[])
        return res
```

### 297. 二叉树的序列化与反序列化
    链接：https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/

    序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。

    请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。

    示例: 

    你可以将以下二叉树：

        1
       / \
      2   3
         / \
        4   5

    序列化为 "[1,2,3,null,null,4,5]"
    提示: 这与 LeetCode 目前使用的方式一致，详情请参阅 LeetCode 序列化二叉树的格式。你并非必须采取这种方式，你也可以采用其他的方法解决这个问题。

    说明: 不要使用类的成员 / 全局 / 静态变量来存储状态，你的序列化和反序列化算法应该是无状态的。

题解一|DFS：
```
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Codec:

    def serialize(self, root):
        """Encodes a tree to a single string.
        
        :type root: TreeNode
        :rtype: str
        """
        if not root:
            return 'X,'
        res=''
        if root:
            res+=str(root.val)+','
            res+=self.serialize(root.left)
            res+=self.serialize(root.right)
            return res

    def serialize(self, root):
        """Encodes a tree to a single string.
        
        :type root: TreeNode
        :rtype: str
        """
        if not root:
            return 'X,'
        left=self.serialize(root.left)
        right=self.serialize(root.right)
        return str(root.val)+','+left+right
            
    def deserialize(self, data):
        """Decodes your encoded data to tree.
        
        :type data: str
        :rtype: TreeNode
        """
        data=data.split(',')
        root=self.buildTree(data)
        return root

    def buildTree(self,data):
        val=data.pop(0)
        if val=='X':
            return None
        node=TreeNode(val)
        node.left=self.buildTree(data)
        node.right=self.buildTree(data)
        return node
        
# Your Codec object will be instantiated and called as such:
# ser = Codec()
# deser = Codec()
# ans = deser.deserialize(ser.serialize(root))
```

题解二|BFS：
```
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

from collections import deque
class Codec:

    def serialize(self, root):
        """Encodes a tree to a single string.
        
        :type root: TreeNode
        :rtype: str
        """
        if not root:
            return
        q=deque()
        q.append(root)
        res=''
        while q:
            node=q.popleft()
            if node:
                res+=str(node.val)+','
                q.append(node.left)
                q.append(node.right)
            else:
                res+='X,'
        return res
            

    def deserialize(self, data):
        """Decodes your encoded data to tree.
        
        :type data: str
        :rtype: TreeNode
        """
        if not data:
            return
        data=data.split(',')
        root=TreeNode(data.pop(0))
        q=[root]
        while q:
            node=q.pop(0)
            if data:
                val=data.pop(0)
                if val != 'X':
                    node.left=TreeNode(val)
                    q.append(node.left)
            if data:
                val=data.pop(0)
                if val != 'X':
                    node.right=TreeNode(val)
                    q.append(node.right)
        return root
            
# Your Codec object will be instantiated and called as such:
# ser = Codec()
# deser = Codec()
# ans = deser.deserialize(ser.serialize(root))
```

### 404. 左叶子之和
    链接：https://leetcode-cn.com/problems/sum-of-left-leaves/

    计算给定二叉树的所有左叶子之和。

    示例：

        3
       / \
      9  20
        /  \
       15   7

    在这个二叉树中，有两个左叶子，分别是 9 和 15，所以返回 24

题解一|递归：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def sumOfLeftLeaves(self, root: TreeNode) -> int:
        if not root:
            return 0
        self.res=0
        def helper(root):
            if not root:
                return 0
            # 注意：是左叶子，而不是左节点。
            if root.left and not root.left.left and not root.left.right: 
                self.res+=root.left.val
            helper(root.left)
            helper(root.right)
        helper(root)
        return self.res
```

```
class Solution:
    def sumOfLeftLeaves(self, root: TreeNode) -> int:
        if not root:
            return 0
        sum = 0
        if root.left and not root.left.left and not root.left.right:
        # if root.left: 题目是左叶子，并非左结点。
            sum += root.left.val
        sum += self.sumOfLeftLeaves(root.left)
        sum += self.sumOfLeftLeaves(root.right)
        return sum
```
题解二|迭代：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def sumOfLeftLeaves(self, root: TreeNode) -> int:
        if not root:
            return 0
        stack=[root]
        res=0
        while stack:
            node=stack.pop()
            if node:
                if node.left and not node.left.left and not node.left.right:
                    res+=node.left.val
                stack.append(node.left)
                stack.append(node.right)
        return res
```

### 429. N叉树的层序遍历
    给定一个 N 叉树，返回其节点值的层序遍历。 (即从左到右，逐层遍历)。

    例如，给定一个 3叉树 :

    返回其层序遍历:

    [
         [1],
         [3,2,4],
         [5,6]
    ]
     
    说明:

    树的深度不会超过 1000。
    树的节点总数不会超过 5000。

题解一|迭代：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""

class Solution:
    def levelOrder(self, root: 'Node') -> List[List[int]]:
        if not root:
            return
        res=[]
        stack=[root]
        depth=0
        while stack:
            res.append([])
            for i in range(len(stack)):
                root=stack.pop(0)
                res[depth].append(root.val)
                for i in root.children:
                    stack.append(i)
            depth+=1
        return res
```

```
"""
# Definition for a Node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""
class Solution:
    def levelOrder(self, root: 'Node') -> List[List[int]]:
        if not root:
            return
        res=[]
        stack=[root]
        depth=0
        while stack:
            res.append([])
            for i in range(len(stack)):
                root=stack.pop(0)
                res[depth].append(root.val)
                stack.extend(root.children)
                # for i in root.children:
                #     stack.append(i)
            depth+=1
        return res
```

```
"""
# Definition for a Node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""

class Solution:
    def levelOrder(self, root: 'Node') -> List[List[int]]:
        if not root:
            return
        res=[]
        stack=[root]
        while stack:
            tmp=[]
            for i in range(len(stack)):
                root=stack.pop(0)
                tmp.append(root.val)
                stack.extend(root.children)
            res.append(tmp)
        return res
```

```
"""
# Definition for a Node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""

class Solution:
    def levelOrder(self, root: 'Node') -> List[List[int]]:
        if not root:
            return
        res=[]
        stack=[root]
        while stack:
            tmp=[]
            res.append([])
            for i in stack:
                res[-1].append(i.val)
                tmp.extend(i.children)
            stack=tmp
        return res
```

题解二|递归：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""
class Solution:
    def levelOrder(self, root: 'Node') -> List[List[int]]:
        res=[]
        def helper(node,depth):
            if not root:
                return
            if len(res)==depth:
                res.append([])
            res[depth].append(node.val)
            for i in node.children:
                helper(i,depth+1)
        helper(root,0)
        return res
```

### 501. 二叉搜索树中的众数
    链接：https://leetcode-cn.com/problems/find-mode-in-binary-search-tree/

    给定一个有相同值的二叉搜索树（BST），找出 BST 中的所有众数（出现频率最高的元素）。

    假定 BST 有如下定义：

    结点左子树中所含结点的值小于等于当前结点的值
    结点右子树中所含结点的值大于等于当前结点的值
    左子树和右子树都是二叉搜索树
    例如：
    给定 BST [1,null,2,2],

       1
        \
         2
        /
       2
    返回[2].

    提示：如果众数超过1个，不需考虑输出顺序

    进阶：你可以不使用额外的空间吗？（假设由递归产生的隐式调用栈的开销不被计算在内）

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def findMode(self, root: TreeNode) -> List[int]:
        hash={}
        def helper(root):
            if root:
                if root.val not in hash:
                    hash[root.val]=1
                else:
                    hash[root.val]+=1
                helper(root.left)
                helper(root.right)
            
        helper(root)
        res=[]
        if not root:
            return 
        count=max(hash.values())
        for k,v in hash.items():
            if count==v:
                res.append(k)
        return res
```

题解二|二叉搜索树处理：

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def findMode(self, root: TreeNode) -> List[int]:
        self.maxTimes=1
        self.curTimes=0
        self.lastNode=None
        self.ans=[]
        def helper(node):
            if node:
                helper(node.left)
                if self.lastNode and self.lastNode.val == node.val:
                    self.curTimes+=1
                else:
                    self.curTimes=1
                self.lastNode=node
                if self.curTimes == self.maxTimes:
                    self.ans.append(node.val)
                elif self.curTimes > self.maxTimes:
                    self.ans=[]
                    self.maxTimes=self.curTimes
                    self.ans.append(node.val)
                helper(node.right)
        helper(root)
        return self.ans
```

题解三|普通二叉树处理：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def findMode(self, root: TreeNode) -> List[int]:
        self.maxTimes=1
        self.hash=dict()
        self.ans=set()
        def helper(node):
            if node:
                helper(node.left)
                num=self.hash.get(node.val,0)+1
                self.hash[node.val]=num
                if num== self.maxTimes:                                             
                    self.ans.add(node.val)
                elif num > self.maxTimes:
                    self.ans.clear()
                    self.ans.add(node.val)
                    self.maxTimes=num
                helper(node.right)
        helper(root)
        return self.ans        
```
### 530. 二叉搜索树的最小绝对差
    链接：https://leetcode-cn.com/problems/minimum-absolute-difference-in-bst/
    给你一棵所有节点为非负值的二叉搜索树，请你计算树中任意两节点的差的绝对值的最小值。

    示例：

    输入：

       1
        \
         3
        /
       2

    输出：
    1

    解释：
    最小绝对差为 1，其中 2 和 1 的差的绝对值为 1（或者 2 和 3）。
     
    提示：

    树中至少有 2 个节点。
    本题与 783 https://leetcode-cn.com/problems/minimum-distance-between-bst-nodes/ 相同

题解一|中序遍历：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def getMinimumDifference(self, root: TreeNode) -> int:
        def inOrder(root):
            if not root:
                return []
            return inOrder(root.left)+[root.val]+inOrder(root.right)

        tmp=inOrder(root)
        res=float('inf')
        for i in range(1,len(tmp)):
            res=min(res,tmp[i]-tmp[i-1])
        return res
```

### 538. 把二叉搜索树转换为累加树
    链接：https://leetcode-cn.com/problems/convert-bst-to-greater-tree/

    给定一个二叉搜索树（Binary Search Tree），把它转换成为累加树（Greater Tree)，使得每个节点的值是原来的节点值加上所有大于它的节点值之和。

    例如：

    输入: 原始二叉搜索树:
                  5
                /   \
               2     13

    输出: 转换为累加树:
                 18
                /   \
              20     13

题解一|递归：
```
class Solution:
    def convertBST(self, root: TreeNode) -> TreeNode:
        self.sum=0
        def helper(root):
            if not root:
                return
            helper(root.right)
            self.sum+=root.val
            root.val=self.sum
            helper(root.left)
        helper(root)
        return root
```

题解二|迭代：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def convertBST(self, root: TreeNode) -> TreeNode:
        sum,stack,node=0,[],root
        while stack or node:
            while node:
                stack.append(node)
                node=node.right
            node=stack.pop()
            sum+=node.val
            node.val=sum
            node=node.left
        return root
```

### 543. 二叉树的直径
    链接：https://leetcode-cn.com/problems/diameter-of-binary-tree/
    
    给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点。

    示例 :
    给定二叉树

              1
             / \
            2   3
           / \     
          4   5    
    返回 3, 它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]。

    注意：两结点之间的路径长度是以它们之间边的数目表示。

题解一|递归：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def diameterOfBinaryTree(self, root: TreeNode) -> int:
        self.res=0
        def helper(root):
            if not root:
                return 0
            left=helper(root.left)
            right=helper(root.right)
            self.res=max(self.res,left+right) # 每个结点都要去判断左子树+右子树的高度是否大于self.max，更新最大值
            return max(left,right)+1
        helper(root)
        return self.res
```

### 559. N叉树的最大深度
    链接：https://leetcode-cn.com/problems/maximum-depth-of-n-ary-tree/

    给定一个 N 叉树，找到其最大深度。

    最大深度是指从根节点到最远叶子节点的最长路径上的节点总数。

    例如，给定一个 3叉树 :

    我们应返回其最大深度，3。

    说明:

    树的深度不会超过 1000。
    树的节点总不会超过 5000。

题解一|递归：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""

class Solution:
    def maxDepth(self, root: 'Node') -> int:
        if not root:
            return 0
        elif not root.children:
            return 1
        else:
            height=[ self.maxDepth(i) for i in root.children]
            return max(height)+1
```

题解二|迭代：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""

class Solution:
    def maxDepth(self, root: 'Node') -> int:
        stack=[]
        if root:
            stack.append((1,root))
        depth=0
        while stack:
            cur,root=stack.pop()
            if root:
                depth=max(depth,cur)
                for i in root.children:
                    stack.append((cur+1,i))
        return depth
```

### 563. 二叉树的坡度
    链接：https://leetcode-cn.com/problems/binary-tree-tilt/
    给定一个二叉树，计算整个树的坡度。

    一个树的节点的坡度定义即为，该节点左子树的结点之和和右子树结点之和的差的绝对值。空结点的的坡度是0。

    整个树的坡度就是其所有节点的坡度之和。

    示例：

    输入：
             1
           /   \
          2     3
    输出：1
    解释：
    结点 2 的坡度: 0
    结点 3 的坡度: 0
    结点 1 的坡度: |2-3| = 1
    树的坡度 : 0 + 0 + 1 = 1
     
    提示：

    任何子树的结点的和不会超过 32 位整数的范围。
    坡度的值不会超过 32 位整数的范围。

题解一|递归：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def findTilt(self, root: TreeNode) -> int:
        self.res=0
        def helper(root):
            if not root:
                return 0
            left = helper(root.left)
            right = helper(root.right)
            self.res+=abs(left-right)
            return left+right+root.val
        
        helper(root)
        return self.res
```

### 572. 另一个树的子树
    链接：https://leetcode-cn.com/problems/subtree-of-another-tree/

    给定两个非空二叉树 s 和 t，检验 s 中是否包含和 t 具有相同结构和节点值的子树。s 的一个子树包括 s 的一个节点和这个节点的所有子孙。s 也可以看做它自身的一棵子树。

    示例 1:
    给定的树 s:

         3
        / \
       4   5
      / \
     1   2
    给定的树 t：

       4 
      / \
     1   2
    返回 true，因为 t 与 s 的一个子树拥有相同的结构和节点值。

    示例 2:
    给定的树 s：

         3
        / \
       4   5
      / \
     1   2
        /
       0
    给定的树 t：

       4
      / \
     1   2
    返回 false。

题解一|递归：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isSubtree(self, s: TreeNode, t: TreeNode) -> bool:
        def helper(s,t):
            if not s and not t:
                return True
            if not s or not t:
                return False
            if s.val != t.val:
                return False
            return helper(s.left,t.left) and helper(s.right,t.right)
        if not s and not t:
            return True
        if not s or not t:
            return False
        return helper(s,t) or self.isSubtree(s.left,t) or self.isSubtree(s.right,t)
```

### 589. N叉树的前序遍历
    链接：https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/

    给定一个 N 叉树，返回其节点值的前序遍历。

    例如，给定一个 3叉树 :

    返回其前序遍历: [1,3,5,6,2,4]。  

    说明: 递归法很简单，你可以使用迭代法完成此题吗?

题解一|递归：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""
class Solution:
    def preorder(self, root: 'Node') -> List[int]:
        ans=[]
        if root:
            ans.append(root.val)
            for i in root.children:
                ans+=self.preorder(i)
        return ans
```

```
"""
# Definition for a Node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""
class Solution:
    def preorder(self, root: 'Node') -> List[int]:
        if not root:
            return 
        res=[]
        def helper(root):
            if not root:
                return
            res.append(root.val)
            for i in root.children:
                helper(i)
        helper(root)
        return res
```

题解二|迭代：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""
class Solution:
    def preorder(self, root: 'Node') -> List[int]:
        if not root:
            return
        stack=[root]
        ans=[]
        while stack:
            root=stack.pop()
            ans.append(root.val)
            for i in root.children[::-1]:
                stack.append(i)
            # stack.extend(root.children[::-1]) # 不使用append
        return ans
```

Python中append和extend的区别：
    
    a=[1,2,3]
    b=[4,5]

    b.extend(a) # 输出[4,5,1,2,3]
    b.append(a) # 输出[4,5,[1,2,3]]

### 590. N叉树的后序遍历
    链接：https://leetcode-cn.com/problems/n-ary-tree-postorder-traversal/

    给定一个 N 叉树，返回其节点值的后序遍历。

    例如，给定一个 3叉树 :

    返回其后序遍历: [5,6,3,2,4,1].

    说明: 递归法很简单，你可以使用迭代法完成此题吗?

题解一|递归：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""

class Solution:
    def postorder(self, root: 'Node') -> List[int]:
        ans=[]
        if root:
            for i in root.children:
                ans+=self.postorder(i)
            ans.append(root.val)
        return ans
```

题解二|迭代：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""

class Solution:
    def postorder(self, root: 'Node') -> List[int]:
        if not root:
            return
        stack=[root]
        res=[]
        while stack:
            root=stack.pop()
            res.append(root.val)
            for i in root.children:
                stack.append(i)
        return res[::-1]
```

### 606. 根据二叉树创建字符串
    链接：https://leetcode-cn.com/problems/construct-string-from-binary-tree/

    你需要采用前序遍历的方式，将一个二叉树转换成一个由括号和整数组成的字符串。

    空节点则用一对空括号 "()" 表示。而且你需要省略所有不影响字符串与原始二叉树之间的一对一映射关系的空括号对。

    示例 1:

    输入: 二叉树: [1,2,3,4]
           1
         /   \
        2     3
       /    
      4     

    输出: "1(2(4))(3)"

    解释: 原本将是“1(2(4)())(3())”，
    在你省略所有不必要的空括号对之后，
    它将是“1(2(4))(3)”。
    示例 2:

    输入: 二叉树: [1,2,3,null,4]
           1
         /   \
        2     3
         \  
          4 

    输出: "1(2()(4))(3)"

    解释: 和第一个示例相似，
    除了我们不能省略第一个对括号来中断输入和输出之间的一对一映射关系。

题解一|递归：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def tree2str(self, t: TreeNode) -> str:
        if not t:
            return ''
        if not t.left and not t.right:
            return str(t.val)
        if not t.left and t.right:
            return str(t.val)+'()'+'('+self.tree2str(t.right)+')'
        if t.left and not t.right:
            return str(t.val)+'('+self.tree2str(t.left)+')'
        return str(t.val)+'('+self.tree2str(t.left)+')'+'('+self.tree2str(t.right)+')'
```

### 617. 合并二叉树
    链接：https://leetcode-cn.com/problems/merge-two-binary-trees/

    给定两个二叉树，想象当你将它们中的一个覆盖到另一个上时，两个二叉树的一些节点便会重叠。

    你需要将他们合并为一个新的二叉树。合并的规则是如果两个节点重叠，那么将他们的值相加作为节点合并后的新值，否则不为 NULL 的节点将直接作为新二叉树的节点。

    示例 1:

    输入: 
        Tree 1                     Tree 2                  
              1                         2                             
             / \                       / \                            
            3   2                     1   3                        
           /                           \   \                      
          5                             4   7                  
    输出: 
    合并后的树:
             3
            / \
           4   5
          / \   \ 
         5   4   7
    注意: 合并必须从两个树的根节点开始。

题解一|递归：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def mergeTrees(self, t1: TreeNode, t2: TreeNode) -> TreeNode:
        if not t1 and not t2:
            return
        if not t1:
            return t2
        if not t2:
            return t1
        if t1 and t2:
            root=TreeNode()
            root.val=t1.val+t2.val
            root.left=self.mergeTrees(t1.left,t2.left)
            root.right=self.mergeTrees(t1.right,t2.right)
        return root
```

### 637.二叉树的层平均值
    链接：https://leetcode-cn.com/problems/average-of-levels-in-binary-tree/

    给定一个非空二叉树, 返回一个由每层节点平均值组成的数组.

    示例 1:

    输入:
        3
       / \
      9  20
        /  \
       15   7
    输出: [3, 14.5, 11]
    解释:
    第0层的平均值是 3,  第1层是 14.5, 第2层是 11. 因此返回 [3, 14.5, 11].
    注意：

    节点值的范围在32位有符号整数范围内。

题解一|迭代：
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def averageOfLevels(self, root: TreeNode) -> List[float]:
        from collections import deque
        if root is None: return
        queue=deque()
        queue.append(root)
        res=[]
        while queue:
            tmp=[]
            for i in range(len(queue)):
                node=queue.popleft()
                tmp.append(node.val)
                s=0
                for each in tmp:
                    s+=each
                s=s/len(tmp)

                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            res.append(s)
        return res
```
去掉脑残for循环，巨耗时。
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def averageOfLevels(self, root: TreeNode) -> List[float]:
        from collections import deque
        if root is None: return
        queue=deque()
        queue.append(root)
        res=[]
        while queue:
            tmp=[]
            for i in range(len(queue)):
                node=queue.popleft()
                tmp.append(node.val)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            res.append(sum(tmp)/len(tmp))
        return res
```

### 671. 二叉树中第二小的节点
    链接：https://leetcode-cn.com/problems/second-minimum-node-in-a-binary-tree/

    给定一个非空特殊的二叉树，每个节点都是正数，并且每个节点的子节点数量只能为 2 或 0。如果一个节点有两个子节点的话，那么该节点的值等于两个子节点中较小的一个。

    更正式地说，root.val = min(root.left.val, root.right.val) 总成立。

    给出这样的一个二叉树，你需要输出所有节点中的第二小的值。如果第二小的值不存在的话，输出 -1 。

    示例 1：

    输入：root = [2,2,5,null,null,5,7]
    输出：5
    解释：最小的值是 2 ，第二小的值是 5 。
    示例 2：

    输入：root = [2,2,2]
    输出：-1
    解释：最小的值是 2, 但是不存在第二小的值。
     
    提示：

    树中节点数目在范围 [1, 25] 内
    1 <= Node.val <= 231 - 1
    对于树中每个节点 root.val == min(root.left.val, root.right.val)

题解一：
```
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution(object):
    def findSecondMinimumValue(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        def helper(root,value):
            if not root:
                return -1
            if root.val > value:
                return root.val
            l=helper(root.left,value)
            r=helper(root.right,value)
            if l==-1:
                return r
            if r==-1:
                return l
            return min(l,r)
        if not root:
            return -1
        return helper(root,root.val)
```


