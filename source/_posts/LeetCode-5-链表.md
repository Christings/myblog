---
title: LeetCode-5-链表
date: 2020-01-15 22:01:02
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
## 五、链表


### 2.两数相加
    链接：https://leetcode-cn.com/problems/add-two-numbers/

    给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

    如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

    您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

    示例：

    输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
    输出：7 -> 0 -> 8
    原因：342 + 465 = 807

题解一：
```
class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        if not l1:
            return l2
        if not l2:
            return l1
        dummy=res=ListNode(0)
        flag=0

        while l1 or l2:
            sum=0
            if l1:
                sum=l1.val
                l1=l1.next
            if l2:
                sum+=l2.val
                l2=l2.next
            tmp=(sum+flag)%10 # 余数
            flag=(sum+flag)//10 # 进位

            res.next=ListNode(tmp)
            res=res.next

            if flag: # 这里虽然创建了一个节点，但是并没有res=res.next,并没有右移。所以插入8时没有问题。同时case输入为[5]和[5]时，可以得到结果[0,1]，而非[0]
                res.next=ListNode(1)
        return dummy.next
```

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        res=dummy=ListNode(-1)
        flag=0
        while l1 and l2:
            res.next=ListNode(l1.val+l2.val+flag)
            flag=res.next.val // 10
            res.next.val %= 10
            res=res.next
            l1=l1.next
            l2=l2.next
        
        p= l1 or l2
        while p:
            res.next=ListNode(p.val+flag)
            flag=res.next.val // 10
            res.next.val %= 10
            p=p.next
            res=res.next
        if flag:
            res.next=ListNode(1)
        return dummy.next
```

### 19. 删除链表的倒数第N个节点
    链接：https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/

    给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。

    示例：

    给定一个链表: 1->2->3->4->5, 和 n = 2.

    当删除了倒数第二个节点后，链表变为 1->2->3->5.
    说明：

    给定的 n 保证是有效的。

    进阶：你能尝试使用一趟扫描实现吗？

题解一|两次遍历：

分析：
    删除从列表开头数起的第 (L−n+1) 个结点，其中 L 是列表的长度。只要我们找到列表的长度 L，这个问题就很容易解决。

    在第一次遍历中，我们找出列表的长度 L。
    设置一个指向哑结点的指针，并移动它遍历列表，直至它到达第 (L−n) 个结点那里。我们把第 (L−n) 个结点的 next 指针重新链接至第 (L−n+2) 个结点，完成这个算法。

时间复杂度：O(L)，该算法对列表进行了两次遍历，首先计算了列表的长度 L 其次找到第 (L−n) 个结点。 操作执行了 2L-n 步，时间复杂度为 O(L)。

空间复杂度：O(1)，我们只用了常量级的额外空间。

```
# Definition for singly-linked list.a
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        dummy=ListNode(-1)
        dummy.next=head # 注意case:[1]和1
        p=dummy
        length=0
        while p:
            p=p.next
            length+=1 # 注意：这里length=6，而不是5，因为有一个头节点。
        length-=(n+1)
        p=dummy
        while length>0:
            p=p.next
            length-=1
        p.next=p.next.next
        return dummy.next
```
题解二|一次遍历：

分析：
    第一个指针从列表的开头向前移动 n+1 步，而第二个指针将从列表的开头出发。

    通过同时移动两个指针向前来保持这个恒定的间隔，直到第一个指针到达最后一个结点。

    此时第二个指针将指向从最后一个结点数起的第 n 个结点。我们重新链接第二个指针所引用的结点的 next 指针指向该结点的下下个结点。

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        dummy=ListNode(-1)
        dummy.next=head # 如果dummy=head,case为[1],1不能通过。
        fast=slow=dummy
        while fast and n:
            fast=fast.next
            n-=1
        while fast.next and slow.next: # 注意不是while fast and slow：
            fast=fast.next
            slow=slow.next

        # print( fast.val,slow.val)
        slow.next=slow.next.next
        return dummy.next
```

### 21. 合并两个有序链表
    链接：c

    将两个有序链表合并为一个新的有序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

    示例：

    输入：1->2->4, 1->3->4
    输出：1->1->2->3->4->4

题解一|递归：

时间复杂度：O(n+m)
空间复杂度：O(n+m)
```
class Solution:
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        if not l1:
            return l2
        elif not l2:
            return l1
        elif l1.val < l2.val:
            l1.next=self.mergeTwoLists(l1.next,l2)
            return l1
        else:
            l2.next=self.mergeTwoLists(l1,l2.next)
            return l2
        

```

题解二|迭代：

时间复杂度：O(n+m)
空间复杂度：O(1)

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        # if not l1:
        #     return l2
        # if not l2:
        #     return l1
        head=dummy=ListNode(-1)
        while l1 and l2:
            if l1.val < l2.val:
                head.next=l1
                l1=l1.next
            else:
                head.next=l2
                l2=l2.next
            head=head.next
        if l1:
            head.next=l1
        if l2:
            head.next=l2
        return dummy.next
```

### 23. 合并K个升序链表
    链接：https://leetcode-cn.com/problems/merge-k-sorted-lists/

    给你一个链表数组，每个链表都已经按升序排列。

    请你将所有链表合并到一个升序链表中，返回合并后的链表。

    示例 1：

    输入：lists = [[1,4,5],[1,3,4],[2,6]]
    输出：[1,1,2,3,4,4,5,6]
    解释：链表数组如下：
    [
    1->4->5,
    1->3->4,
    2->6
    ]
    将它们合并到一个有序链表中得到。
    1->1->2->3->4->4->5->6
    示例 2：

    输入：lists = []
    输出：[]
    示例 3：

    输入：lists = [[]]
    输出：[]
    

    提示：

    k == lists.length
    0 <= k <= 10^4
    0 <= lists[i].length <= 500
    -10^4 <= lists[i][j] <= 10^4
    lists[i] 按 升序 排列
    lists[i].length 的总和不超过 10^4

题解一|两两合并：

时间复杂度：O(k^2*n)
空间复杂度：O(1)

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeKLists(self, lists: List[ListNode]) -> ListNode:

        def mergeTwoLists(l1, l2):
            head=dummy=ListNode(-1)
            while l1 and l2:
                if l1.val < l2.val:
                    head.next=l1
                    l1=l1.next
                else:
                    head.next=l2
                    l2=l2.next
                head=head.next
            if l1:
                head.next=l1
            if l2:
                head.next=l2
            return dummy.next

        ans=ListNode(float('-inf'))
        for i in lists:
            ans=mergeTwoLists(ans,i)
        return ans.next
```

题解二|归并：

时间复杂度：O(kn*logk)
空间复杂度：O(logk)

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeKLists(self, lists: List[ListNode]) -> ListNode:

        def mergeTwoLists(l1, l2):
            head=dummy=ListNode(-1)
            while l1 and l2:
                if l1.val < l2.val:
                    head.next=l1
                    l1=l1.next
                else:
                    head.next=l2
                    l2=l2.next
                head=head.next
            if l1:
                head.next=l1
            if l2:
                head.next=l2
            return dummy.next

        def merge(lists,left,right):
            if left==right:
                return lists[left]
            if left>right:
                return
            mid=left+(right-left)//2
            return mergeTwoLists(merge(lists,left,mid),merge(lists,mid+1,right))
        
        if not lists:
            return
        return merge(lists,0,len(lists)-1)
```

题解三|优先队列|小顶堆：

时间复杂度：O(kn*logk)
空间复杂度：O(k)

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
import heapq

class Solution:
    def mergeKLists(self, lists: List[ListNode]) -> ListNode:
        p=dummy=ListNode(0)
        head=[]
        for i in range(len(lists)):
            if lists[i]:
                heapq.heappush(head,(lists[i].val,i))
                lists[i]=lists[i].next
        while head:
            val,index=heapq.heappop(head)
            p.next=ListNode(val)
            p=p.next
            if lists[index]:
                heapq.heappush(head,(lists[index].val,index))
                lists[index]=lists[index].next
        return dummy.next
```

### 24. 两两交换链表中的节点
    链接：https://leetcode-cn.com/problems/swap-nodes-in-pairs/

    给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

    你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

    示例:

    给定 1->2->3->4, 你应该返回 2->1->4->3.

参考：https://leetcode-cn.com/problems/swap-nodes-in-pairs/solution/dong-hua-yan-shi-24-liang-liang-jiao-huan-lian-bia/

题解一|递归：
分析：

    从链表的头节点 head 开始递归。
    每次递归都负责交换一对节点。由 firstNode 和 secondNode 表示要交换的两个节点。
    下一次递归则是传递的是下一对需要交换的节点。若链表中还有节点，则继续递归。
    交换了两个节点以后，返回 secondNode，因为它是交换后的新头。
    在所有节点交换完成以后，我们返回交换后的头，实际上是原始链表的第二个节点。

时间复杂度：O(N)，其中 N 指的是链表的节点数量。
空间复杂度：O(N)，递归过程使用的堆栈空间。
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def swapPairs(self, head: ListNode) -> ListNode:
        if not head or not head.next:
            return head

        first=head
        second=head.next

        # swap
        first.next=self.swapPairs(second.next)
        second.next=first

        return second
```
题解二|迭代：

我们把链表分为两部分，即奇数节点为一部分，偶数节点为一部分，A 指的是交换节点中的前面的节点，B 指的是要交换节点中的后面的节点。在完成它们的交换，我们还得用 prevNode 记录 A 的前驱节点。

思路：

    1、firstNode（即 A） 和 secondNode（即 B） 分别遍历偶数节点和奇数节点，即两步看作一步。

    2、交换两个节点：
        firstNode.next = secondNode.next
        secondNode.next = firstNode

    3、还需要更新 prevNode.next 指向交换后的头。
        prevNode.next = secondNode

    4、迭代完成后得到最终的交换结果。

时间复杂度：O(N)，其中 N 指的是链表的节点数量。
空间复杂度：O(1)。

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def swapPairs(self, head: ListNode) -> ListNode:
        dummy=ListNode(-1)
        dummy.next=head
        pre=dummy

        while head and head.next:
            first=head
            second=head.next

            # swap
            pre.next=second
            first.next=second.next
            second.next=first

            # Reinitializing the head and prev_node for next swap
            pre=first # 通过pre来构成新的链表
            # print(pre.val)
            head=first.next

        return dummy.next
```

### 25. K 个一组翻转链表
    链接：https://leetcode-cn.com/problems/reverse-nodes-in-k-group/

    给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。

    k 是一个正整数，它的值小于或等于链表的长度。

    如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

    示例：

    给你这个链表：1->2->3->4->5

    当 k = 2 时，应当返回: 2->1->4->3->5

    当 k = 3 时，应当返回: 3->2->1->4->5

    说明：

    你的算法只能使用常数的额外空间。
    你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

题解一|模拟：

时间复杂度为 O(n∗K)，最好的情况为 O(n) ，最差的情况未 O(n^2)
空间复杂度为 O(1) 
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        dummy=ListNode(-1)
        dummy.next=head
        pre=dummy
        end=dummy

        while end.next:
            i=0
            while i<k and end:
                end=end.next
                i+=1
            if not end: break
            
            # 断开链表
            start=pre.next
            nxt=end.next
            end.next=None

            pre.next=self.reverse(start) # 翻转
            start.next=nxt # 链接断开的链表

            # 后移处理下一组
            pre=start
            end=pre

        return dummy.next
    def reverse(self, head):
            if not head:
                return
            pre=None
            while head:
                nxt=head.next
                head.next=pre
                pre=head
                head=nxt
            return pre
        
```

### 61. 旋转链表
    链接：https://leetcode-cn.com/problems/rotate-list/

    给定一个链表，旋转链表，将链表每个节点向右移动 k 个位置，其中 k 是非负数。

    示例 1:

    输入: 1->2->3->4->5->NULL, k = 2
    输出: 4->5->1->2->3->NULL
    解释:
    向右旋转 1 步: 5->1->2->3->4->NULL
    向右旋转 2 步: 4->5->1->2->3->NULL
    示例 2:

    输入: 0->1->2->NULL, k = 4
    输出: 2->0->1->NULL
    解释:
    向右旋转 1 步: 2->0->1->NULL
    向右旋转 2 步: 1->2->0->NULL
    向右旋转 3 步: 0->1->2->NULL
    向右旋转 4 步: 2->0->1->NULL

题解一|暴力法：

分析：
    
    首先遍历获得链表的长度以及链表尾部的指针（引用）；
    之后头尾拼接起来；
    随后我们只要从头遍历k - k % length步就能够找到旋转后链表的头指针，断开尾指针和头指针，返回头指针。

关键点就是k，如果移动一次就相当于将倒数第一个元素1移到链表开头，如果移动两次就相当于将倒数第二和倒数第一个元素2和1移动到链表开头。

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/linknode1.jpg](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/linknode1.jpg)

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/linknode2.jpg](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/linknode2.jpg)

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/linknode3.jpg](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/linknode3.jpg)
```
class Solution:
    def rotateRight(self, head: ListNode, k: int) -> ListNode:
        if not head:
            return None
        cur=head
        length=1
        while cur.next:
            cur=cur.next
            length+=1
        cur.next=head # 头尾拼接
        k=length-k%length                                          
        while k:
            k-=1
            cur=cur.next
        head=cur.next # 找到头指针
        cur.next=None  # 断开尾节点             
        return head
```

### 82.删除排序链表中的重复元素 II
    链接：https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/

    给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 没有重复出现 的数字。

    示例 1:

    输入: 1->2->3->3->4->4->5
    输出: 1->2->5
    示例 2:

    输入: 1->1->1->2->3
    输出: 2->3

题解一：

思路：

建立哑结点dummy，当前处理节点为curr，它的值为anchor，它的前序节点为prev。

如果curr的后序节点的值等于anchor，那么标注flag为True，curr一直后移下去，总之，curr移到连续的最后一个值为anchor的节点，停下来。如果经历了上述步骤，那么prev和curr.next相连，prev按兵不动，curr后移一位；如果没有经历，curr的值是独一无二的，那么prev和curr同时右移一位。继续循环直到curr移到最后一位。

时间复杂度: O(n), 空间复杂度: O(1)

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        dummy=ListNode(-1)
        dummy.next=head
        pre,curr=dummy,head

        while curr:
            anchor=curr.val
            flag=False
            while curr.next:
                if curr.next.val == anchor:
                    curr=curr.next
                    flag=True
                else:
                    break
            if flag:
                pre.next=curr.next # pre并未向前移动
            else:
                pre=curr
            curr=curr.next
        return dummy.next
```

题解二|快慢指针：

    1. fast 遍历链表,让fast 去嗅探不相等元素
    2. slow.next == fast --> slow 与 fast 之间没有重复元素，slow 动。
    3. slow.next != fast --> slow 与 fast 之间存在重复元素，让slow指向的元素跳过这些重复元素，slow 不动。

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        dummy=ListNode(-1)
        dummy.next=head
        slow=dummy
        fast=head

        while fast and fast.next:
            if fast.val != fast.next.val:
                if slow.next == fast: # 注意此处判断的是内存地址，而非节点的值
                    slow=fast
                    # print('111',slow)
                else:
                    # print('222',fast.next)
                    slow.next=fast.next # slow并未向前移动
            fast=fast.next
        if slow.next != fast: # 解决case:[1,1]
            slow.next=fast.next
        return dummy.next
```

```
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        if head == None or head.next == None:
            return head
        dummy = ListNode(-1000)
        dummy.next = head
        slow = dummy
        fast = dummy.next
        while fast:
            if  fast.next and fast.next.val == fast.val:
                tmp = fast.val
                while fast and tmp == fast.val:
                    fast = fast.next
            else:
                slow.next = fast
                slow = fast
                fast = fast.next
        slow.next = fast
        return dummy.next
```

```
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        if head == None or head.next == None:
            return head
        dummy = ListNode(-1)
        dummy.next = head
        slow = dummy
        fast = dummy.next
        while fast:
            while fast.next and slow.next.val == fast.next.val:
                fast = fast.next
            if slow.next == fast:
                slow = fast
            else:
                slow.next = fast.next
            fast = fast.next
        return dummy.next
```

题解三|递归：
```
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        if not head:
            return head
        if head.next and head.val == head.next.val:
            while head.next and head.val == head.next.val:
                head=head.next
            return self.deleteDuplicates(head.next)
        else:
            head.next=self.deleteDuplicates(head.next) 
        return head
```

### 83. 删除排序链表中的重复元素
    链接：https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/

    给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

    示例 1:

    输入: 1->1->2
    输出: 1->2
    示例 2:

    输入: 1->1->2->3->3
    输出: 1->2->3

思路：因为是排序列表，通过将结点的值与它之后的结点进行比较来确定它是否为重复结点。如果它是重复的，我们更改当前结点的 next 指针，以便它跳过下一个结点并直接指向下一个结点之后的结点。

时间复杂度：O(n)，因为列表中的每个结点都检查一次以确定它是否重复，所以总运行时间为 O(n)，其中 n 是列表中的结点数。
空间复杂度：O(1)，没有使用额外的空间。

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        dummy=ListNode(-1)
        dummy.next=head
        while head and head.next:
            if head.val == head.next.val:
                head.next=head.next.next
            else:
                head=head.next
        return dummy.next
```

```
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        dummy=head
        while head and head.next:
            if head.val == head.next.val:
                head.next=head.next.next
            else:
                head=head.next
        return dummy
```

### 86. 分隔链表
    链接：https://leetcode-cn.com/problems/partition-list/

    给定一个链表和一个特定值 x，对链表进行分隔，使得所有小于 x 的节点都在大于或等于 x 的节点之前。

    你应当保留两个分区中每个节点的初始相对位置。

    示例:

    输入: head = 1->4->3->2->5->2, x = 3
    输出: 1->2->2->4->3->5

分析：

    哈哈，差点没看懂题。

这道题要求我们划分链表，把所有小于给定值的节点都移到前面，大于该值的节点顺序不变，相当于一个局部排序的问题。那么可以想到的一种解法是首先找到第一个大于或等于给定值的节点，用题目中给的例子来说就是先找到4，然后再找小于3的值，每找到一个就将其取出置于4之前即可

题解一|双指针法：
    
用两个指针before 和 after 来追踪上述的两个链表。两个指针可以用于分别创建两个链表，然后将这两个链表连接即可获得所需的链表。

时间复杂度: O(N)，其中N是原链表的长度，我们对该链表进行了遍历。
空间复杂度: O(1)，我们没有申请任何新空间。值得注意的是，我们只移动了原有的结点，因此没有使用任何额外空间。

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def partition(self, head: ListNode, x: int) -> ListNode:
        before=bf=ListNode(-1)
        after=af=ListNode(-1)

        while head:
            if head.val < x:
                before.next=head
                before=before.next
            else:
                after.next=head
                after=after.next
            head=head.next
        
        after.next=None
        before.next=af.next
        return bf.next
```

### 92. 反转链表 II
    链接：https://leetcode-cn.com/problems/reverse-linked-list-ii/
    
    反转从位置 m 到 n 的链表。请使用一趟扫描完成反转。

    说明:
    1 ≤ m ≤ n ≤ 链表长度。

    示例:

    输入: 1->2->3->4->5->NULL, m = 2, n = 4
    输出: 1->4->3->2->5->NULL

题解一|迭代：

时间复杂度: O(N)。考虑包含 N 个结点的链表。对每个节点最多会处理
（第 n 个结点之后的结点不处理）。

空间复杂度: O(1)。我们仅仅在原有链表的基础上调整了一些指针，只使用了 O(1)的额外存储空间来获得结果。

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reverseBetween(self, head: ListNode, m: int, n: int) -> ListNode:
        if not head:
            return None
        prev,cur=None,head
        while m>1:
            prev=cur
            cur=cur.next
            m,n=m-1,n-1
        # print(cur.val,prev.val)
        # tail 指针指向从链表头起的第m个结点，此结点是反转后链表的尾部，故称为 tail。
        # con 指针指向第 m 个结点的前一个结点，此结点是新链表的头部。
        con,tail=prev,cur
        while n:
            next=cur.next
            cur.next=prev
            prev=cur
            cur=next
            n-=1
        if con:
            con.next=prev # 头节点链接反转后链表的头节点
        else:
            # head.next=prev
            head=prev
        tail.next=cur # 反转后的链表链接尾节点
        return head
```

题解二｜一次遍历：

```
def reverseBetween(self,head, m, n):

        if not head:
            return
        dummy = ListNode(-1)
        dummy.next = head
        pre = dummy
        for _ in range(m-1):
            pre = pre.next
        cur = pre.next
        for _ in range(n-m):
            nxt=cur.next
            cur.next=nxt.next
            nxt.next=pre.next # 注意：这里不能使用cur，因为整个过程pre和cur不会移动。
            pre.next=nxt
        return dummy.next
```

### 141.环形链表
    链表：https://leetcode-cn.com/problems/linked-list-cycle

    给定一个链表，判断链表中是否有环。

    为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。

    示例 1：

    输入：head = [3,2,0,-4], pos = 1
    输出：true
    解释：链表中有一个环，其尾部连接到第二个节点。


    示例 2：

    输入：head = [1,2], pos = 0
    输出：true
    解释：链表中有一个环，其尾部连接到第一个节点。


    示例 3：

    输入：head = [1], pos = -1
    输出：false
    解释：链表中没有环。

    进阶：你能用 O(1)（即，常量）内存解决此问题吗？

题解一|hash:
    
    时间复杂度：O(n)，对于含有 n个元素的链表，我们访问每个元素最多一次。添加一个结点到哈希表中只需要花费 O(1) 的时间。

    空间复杂度：O(n)，空间取决于添加到哈希表中的元素数目，最多可以添加 n 个元素。

```
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None

class Solution:
    def hasCycle(self, head: ListNode) -> bool:
        hash={}
        while head :
            if head not in hash:
                hash[head]=0
                head=head.next
            else:
                return True
        return False
```
题解二|快慢指针:

```
class Solution:
    def hasCycle(self, head: ListNode) -> bool:
        if not head or not head.next: 
            return False
        i,j=head,head.next
        while j and j.next: # 如果没有j.next，无法通过case ([1,2] -1).
            if i==j:
                return True
            i,j=i.next,j.next.next
        return False
```

```
class Solution:
    def hasCycle(self, head: ListNode) -> bool:
        slow=fast=head
        while fast and fast.next: # 如果没有fast.next，无法通过case ([1] -1).
            fast=fast.next.next
            slow=slow.next
            # print(fast.val,slow.val)
            if fast == slow:
                # print(fast.val)
                return True # 返回True的节点并不一定就是环的入口节点。
        return False
```
### 142.环形链表 II
    链接：https://leetcode-cn.com/problems/linked-list-cycle-ii/

    给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

    为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。

    说明：不允许修改给定的链表。

    示例 1：

    输入：head = [3,2,0,-4], pos = 1
    输出：tail connects to node index 1
    解释：链表中有一个环，其尾部连接到第二个节点。

    示例 2：

    输入：head = [1,2], pos = 0
    输出：tail connects to node index 0
    解释：链表中有一个环，其尾部连接到第一个节点。


    示例 3：

    输入：head = [1], pos = -1
    输出：no cycle
    解释：链表中没有环。

    进阶：你是否可以不用额外空间解决此题？

题解一|hash：
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def detectCycle(self, head: ListNode) -> ListNode:
        hash={}
        while head:
            if head not in hash:
                hash[head]=0
                head=head.next
            else:
                return head
        return None
```
题解二|快慢指针:
```
class Solution:
    def detectCycle(self, head: ListNode) -> ListNode:
        if not head or not head.next:
            return None
        slow,fast=head,head
        while fast and fast.next:
            slow=slow.next
            fast=fast.next.next
            if slow == fast:
                slow=head
                while slow != fast:
                    slow=slow.next
                    fast=fast.next
                return slow
        return None
```

### 143. 重排链表
    链接：https://leetcode-cn.com/problems/reorder-list

    给定一个单链表 L：L0→L1→…→Ln-1→Ln ，
    将其重新排列后变为： L0→Ln→L1→Ln-1→L2→Ln-2→…

    你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

    示例 1:

    给定链表 1->2->3->4, 重新排列为 1->4->2->3.
    示例 2:

    给定链表 1->2->3->4->5, 重新排列为 1->5->2->4->3.

题解一|反转链表:

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/linknode4.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/linknode4.png)

思路：

    找中点
    翻转中点之后的链表
    依次拼接

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reorderList(self, head: ListNode) -> None:
        """
        Do not return anything, modify head in-place instead.
        """
        # 1、找中点, 偶数个时找上界
        if not head or not head.next:
            return head
        slow,fast=head,head
        while fast and fast.next:
            slow=slow.next
            fast=fast.next.next
        mid=slow        
        # 2、翻转中点之后的链表,采用是pre, slow双指针方法
        pre=None
        while slow:
            next=slow.next
            slow.next=pre
            pre=slow
            slow=next
        # 3、翻转链表和前面链表拼接
        p1=head # 链表头
        p2=pre # 翻转头

        # print(mid.val,p2.val)
        while p2 != mid:
            next1,next2=p1.next,p2.next
            p1.next=p2               
            p2.next=next1
            p1,p2=next1,next2
        return head
```

题解二|栈：
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reorderList(self, head: ListNode) -> None:
        """
        Do not return anything, modify head in-place instead.
        """
        if not head:
            return head
        dummy=head
        stack=[]
        while dummy:
            stack.append(dummy)
            dummy=dummy.next
        n=len(stack)
        mid=(n-1)//2
        
        dummy=head
        while mid:
            tmp=stack.pop() # 弹出链表最后一个节点
            tmp.next=dummy.next
            dummy.next=tmp
            dummy=tmp.next # 移动2个位置，使用dummy=dummy.next.next也可以。
            mid-=1
        stack.pop().next=None
        return head
```

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reorderList(self, head: ListNode) -> None:
        """
        Do not return anything, modify head in-place instead.
        """
        if not head:
            return head
        fast,slow=head,head
        stack=[]
        while fast.next and fast.next.next:
            fast=fast.next.next
            slow=slow.next
        while slow.next:
            stack.append(slow.next)
            slow.next=slow.next.next
        dummy=head
        while stack:
            tmp=stack.pop()
            tmp.next=dummy.next
            dummy.next=tmp
            dummy=dummy.next.next
        return head
```

### 147. 对链表进行插入排序
    链接：https://leetcode-cn.com/problems/insertion-sort-list

    对链表进行插入排序。


    插入排序的动画演示如上。从第一个元素开始，该链表可以被认为已经部分排序（用黑色表示）。
    每次迭代时，从输入数据中移除一个元素（用红色表示），并原地将其插入到已排好序的链表中。


    插入排序算法：

    插入排序是迭代的，每次只移动一个元素，直到所有元素可以形成一个有序的输出列表。
    每次迭代中，插入排序只从输入数据中移除一个待排序的元素，找到它在序列中适当的位置，并将其插入。
    重复直到所有输入数据插入完为止。
     

    示例 1：

    输入: 4->2->1->3
    输出: 1->2->3->4
    示例 2：

    输入: -1->5->3->4->0
    输出: -1->0->3->4->5

参考：https://blog.csdn.net/qq_17550379/article/details/80708238

数组的插入排序：
```
def insertSort(lists):
    count=len(lists)
    for i in range(1,count):
        key=lists[i]
        j=i-1
        while j>=0:
            if lists[j]>key:
                lists[j+1]=lists[j]
                lists[j]=key
            j-=1
    return lists
```

题解一：

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def insertionSortList(self, head: ListNode) -> ListNode:
        if not head or not head.next:
            return head
        cur,nxt=head,head.next
        dummy=ListNode(float('-inf'))
        dummy.next=head

        while nxt:
            if nxt.val >= cur.val:
                cur=cur.next
                nxt=nxt.next
            else:
                cur.next=nxt.next # 断链

                pre1,pre2=dummy,dummy.next # 寻找插入位置
                while nxt.val > pre2.val:
                    pre1=pre2
                    pre2=pre2.next

                pre1.next=nxt
                nxt.next=pre2

                nxt=cur.next # 再从第一个值开始循环
        return dummy.next
```


题解二：

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def insertionSortList(self, head: ListNode) -> ListNode:
        if head == None or head.next == None:
            return head

        dummy = ListNode(-1)
        dummy.next = head
        pre = dummy
        cur = head
        while cur:
            lat = cur.next
            if lat and lat.val < cur.val:  # 只有cur.next比cur小的时候我们才寻找插入点
                while pre.next and pre.next.val < lat.val:
                    pre = pre.next
                tmp = pre.next
                pre.next = lat
                cur.next = lat.next
                lat.next = tmp
                pre = dummy
            else:
                cur = lat

        return dummy.next
```

？题解三：
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def insertionSortList(self, head: ListNode) -> ListNode:
        p=dummy=ListNode(-1)
        curr=dummy.next=head
        while curr and curr.next:
            val = curr.next.val
            if curr.val < val:
                curr=curr.next
                continue
            if p.next.val > val:
                p=dummy
            while p.next.val < val:
                p=p.next
            new=curr.next
            curr.next=new.next
            new.next=p.next
            p.next=new
        return dummy.next
```

### 160. 相交链表
    链接：https://leetcode-cn.com/problems/intersection-of-two-linked-lists/

题解一|暴力:

对链表A中的每一个结点 ai，遍历整个链表 B 并检查链表 B 中是否存在结点和 a 
i相同。

时间复杂度 : (mn)
空间复杂度 : O(1)

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        p,q=headA,headB
        while p:
            while q:
                if p == q:
                    return q
            q=q.next
        p=p.next
        return None
```

题解二|hash：
    
分析：

    遍历链表 A 并将每个结点的地址/引用存储在哈希表中。然后检查链表 B 中的每一个结点 bi 是否在哈希表中。若在，则 bi为相交结点。

时间复杂度 : O(m+n)。
空间复杂度 : O(m) 或 O(n)。

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        p,q=headA,headB
        hash={}
        while p:
            if p not in hash:
                hash[p]=1
            p=p.next
        while q:
            if q in hash:
                return q
            q=q.next
```

题解三|双指针：

分析：
    两个指针分别从链表头开始扫描，每次分别走一步
    若指针走到null，则从另一个链表头部开始走
    两指针相同时:
        (利用此时两个指针移动步数一致)
        指针不为null：指针位置为相遇点
        指针为null：两个链表不相交

    创建两个指针 pA 和 pB，分别初始化为链表 A 和 B 的头结点。然后让它们向后逐结点遍历。

    当 pA 到达链表的尾部时，将它重定位到链表 B 的头结点 (你没看错，就是链表 B); 类似的，当 pB 到达链表的尾部时，将它重定位到链表 A 的头结点。

    若在某一时刻 pA 和 pB 相遇，则 pA/pB 为相交结点。

    想弄清楚为什么这样可行, 可以考虑以下两个链表: A={1,3,5,7,9,11} 和 B={2,4,9,11}，相交于结点 9。 由于 B.length (=4) < A.length (=6)，pB 比 pA少经过 2 个结点，会先到达尾部。将 pB 重定向到 A 的头结点，pA 重定向到 B 的头结点后，pB 要比 pA 多走 2 个结点。因此，它们会同时到达交点。

    如果两个链表存在相交，它们末尾的结点必然相同。因此当 pA/pB 到达链表结尾时，记录下链表 A/B 对应的元素。若最后元素不相同，则两个链表不相交。

时间复杂度 : O(m+n)
空间复杂度 : O(1)

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        p,q=headA,headB
        while p != q:
            if p:
                p=p.next
            else:
                p=headB
            if q:
                q=q.next
            else:
                q=headA
        return p
```

### 203. 移除链表元素
    链接：https://leetcode-cn.com/problems/remove-linked-list-elements/

    删除链表中等于给定值 val 的所有节点。

    示例:

    输入: 1->2->6->3->4->5->6, val = 6
    输出: 1->2->3->4->5

题解一|删除头结点时另做考虑：

思路：删除头结点时另做考虑，由于头结点没有前一个结点。

```
class Solution:
    def removeElements(self, head: ListNode, val: int) -> ListNode:
        if not head:
            return head
        # 如果不对头结点单独考虑，无法解决case：[1]和1
        while head and head.val == val:
            head=head.next
        dummy=head
        while head and head.next:
            if head.next.val == val:
                # print(val)
                # head=head.next.next
                head.next=head.next.next
            else:
                head=head.next
        return dummy
```

题解二：
```
时间复杂度：O(N)，只遍历了一次
空间复杂度：O(1)

class Solution:
    def removeElements(self, head: ListNode, val: int) -> ListNode:
        dummy=ListNode(0)
        dummy.next=head

        pre,curr=dummy,head
        # print(pre.val,curr.val) 0和1
        while curr:
            if curr.val == val:
                pre.next=curr.next
            else:
                pre=curr
            curr=curr.next
            
        return dummy.next
```

```
class Solution:
    def removeElements(self, head: ListNode, val: int) -> ListNode:
        dummy=ListNode(0)
        dummy.next=head
        pre=dummy
        while pre.next:
            if pre.next.val == val:
                pre.next=pre.next.next
            else:
                pre=pre.next
            
        return dummy.next
```

题解三|递归：
```
class Solution:
    def removeElements(self, head: ListNode, val: int) -> ListNode:
        if not head:
            return head
        head.next=self.removeElements(head.next,val) # 不能移动到代码最后        
        if head.val==val:
            return head.next
        else:
            return head
```

### 206.反转链表
    链接：https://leetcode-cn.com/problems/reverse-linked-list/

    反转一个单链表。

    示例:

    输入: 1->2->3->4->5->NULL
    输出: 5->4->3->2->1->NULL
    进阶:
    你可以迭代或递归地反转链表。你能否用两种方法解决这道题？

参考：
    [https://leetcode.com/problems/reverse-linked-list/solution/](https://leetcode.com/problems/reverse-linked-list/solution/)  
     [http://baijiahao.baidu.com/s?id=1574261186522385&wfr=spider&for=pc](http://baijiahao.baidu.com/s?id=1574261186522385&wfr=spider&for=pc)  
     [https://blog.csdn.net/autumn20080101/article/details/7607148](https://blog.csdn.net/autumn20080101/article/details/7607148)  
     [https://www.cnblogs.com/scud001/p/4423324.html](https://www.cnblogs.com/scud001/p/4423324.html)

    初始状态，prev是NULL，head指向当前的头节点A，next指向A节点的下一个节点B。
    首先从A节点开始逆序，将A节点的next指针指向prev，因为prev的当前值是NULL，所以A节点就从链表中脱离出来了，然后移动
    head和next指针，使它们分别指向B节点和B的下一个节点C（因为当前的next已经指向B节点了，因此修改A节点的next指针不会
    导致链表丢失）。逆向节点A之后，链表的状态如图（2）所示：

     从图（1）的初始状态到图（2）状态共做了四个操作，这四个操作的伪代码如下：

    head->next = prev;
    
    prev = head;
    
    head = next;
    
    next = head->next;

    这四行伪代码就是循环算法的迭代体了，现在用这个迭代体对图（2）的状态再进行一轮迭代，就得到了图（3）的状态：

    那么循环终止条件呢？现在对图（3）的状态再迭代一次得到图（4）的状态：
    
    此时可以看出，在图（4）的基础上再进行一次迭代就可以完成链表的逆序，因此循环迭代的终止条件就是当前的head指针是NULL。

    现在来总结一下，循环的初始条件是：

    prev = NULL;
    
    循环迭代体是：

    //在头节点改变之前，先获取下一个节点的指针
    next = head->Next;
    //头节点的下一个节点要改成它的上一个节点，是一个逆转的过程
    head->Next = prev;
    //上一个节点前移指向头节点
    prev = head;
    //头节点前移指向下一个节点
    head = next;

    循环终止条件是：

    head == NULL

![](http://p2lakvkq0.bkt.clouddn.com/linkedlist.jpg)


题解一|指针迭代：

反转一个节点的时候，把一个节点的后驱改为指向它前驱就可以了。这里需要注意的点就是，当你把当前节点的后驱指向前驱的时候，这个时候链表会被截断，也就是说后面的节点和当前节点分开了，所以我们需要一个变量来保存当前节点的后驱，以访丢失。

第一个指针叫 pre，最初是指向 null 的。
第二个指针 cur 指向 head，然后不断遍历 cur。
每次迭代到 cur，都将 cur 的 next 指向 pre，然后 pre 和 cur 前进一位。
都迭代完了(cur 变成 null 了)，pre 就是最后一个节点了。

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/linknode5.JPG](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/linknode5.JPG)

参考：https://blog.csdn.net/baidu_31657889/article/details/91552141

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reverseList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        prve = None
        while head:
            next = head.next # 记录当前节点的下一个节点
            head.next = prve # 将当前节点指向pre
            prve = head # pre节点和head节点前进一位
            head = next
        return prve
```

```
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        pre=None
        while head:
            pre, pre.next, head = head, pre, head.next
        return pre
```

???题解二|递归:

递归的两个条件：

    终止条件是当前节点或者下一个节点==null
    在函数内部，改变节点的指向，也就是 head 的下一个节点指向 head 递归函数那句

    很不好理解，其实就是 head 的下一个节点指向head。

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        if head is None or head.next is None:  
            return head  # 递归终止条件是当前为空，或者下一个节点为空
        cur=self.reverseList(head.next) # cur是最后一个节点5
        head.next.next=head 
        head.next=None
        return cur

```

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        def helper(head):
            if head is None or head.next is None:
                return head,head
            pre,last=helper(head.next)
            last.next=head
            head.next=None
            return pre,head
        res,_=helper(head)
        return res,_
```

问题扩展：部分节点进行反转

### 234.回文链表
    链接：https://leetcode-cn.com/problems/palindrome-linked-list/

    请判断一个链表是否为回文链表。

    示例 1:

    输入: 1->2
    输出: false
    示例 2:

    输入: 1->2->2->1
    输出: true
    进阶：
    你能否用 O(n) 时间复杂度和 O(1) 空间复杂度解决此题？

数组列表底层是使用数组存储值，我们可以通过索引在 O(1)的时间访问列表任何位置的值，这是由于内存寻址的方式。

链表存储的是称为节点的对象，每个节点保存一个值和指向下一个节点的指针。访问某个特定索引的节点需要 O(n) 的时间，因为要通过指针获取到下一个位置的节点。

题解一|数组：

分析：

确定数组列表是否为回文很简单，我们可以使用双指针法来比较两端的元素，并向中间移动。一个指针从起点向中间移动，另一个指针从终点向中间移动。这需要 O(n)的时间，因为访问每个元素的时间是 O(1)，而有 n 个元素要访问。

然后，直接在链表上操作并不简单，因为不论是正向访问还是反向访问都不是 O(1)。而将链表的值复制到数组列表中是 O(n)，因此最简单的方法就是将链表的值复制到数组列表中，再使用双指针法判断。

算法：

    复制链表值到数组列表中。
    使用双指针法判断是否为回文。

第一步，我们需要遍历链表将值复制到数组列表中。我们用 currentNode 指向当前节点。每次迭代向数组添加 currentNode.val，并更新 currentNode = currentNode.next，当 currentNode = null 则停止循环。

执行第二部的最佳方法取决于你使用的编程语言。在 Python 中，很容易构造一个列表的反向副本，也很容易比较两个列表。在其他语言中，就没有那么简单。因此最好使用双指针法来检查是否为回文。我们在起点放置一个指针，在结尾放置一个指针，每一次迭代判断两个指针指向的元素是否相同，若不同，返回 false；相同则将两个指针向内移动，并继续判断，直到相遇。

在编码的过程中，注意我们比较的是节点值的大小，而不是节点本身。正确的比较方式是：node_1.val==node_2.val，node_1==node_2 是错误的。

复杂度：

时间复杂度：O(n)，其中 n 指的是链表的元素个数。
    
    第一步： 遍历链表并将值复制到数组中，O(n)。
    第二步：双指针判断是否为回文，执行了 O(n/2) 次的判断，即 O(n)。
    总的时间复杂度：O(2n) = O(n)
空间复杂度：O(n)，其中 n 指的是链表的元素个数，我们使用了一个数组列表存放链表的元素值。

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def isPalindrome(self, head: ListNode) -> bool:
        arr=[]
        while head:
            arr.append(head.val)
            head=head.next
        return arr==arr[::-1]
```

快慢指针：

```
        l = 0
        r = len(tmp)-1
        while l<r:
            if tmp[l] != tmp[r]:
                return False
            l += 1
            r -= 1
        return True
```

题解二|快慢指针+反转链表：

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def isPalindrome(self, head: ListNode) -> bool:
        def reverseList(head):
            pre=None
            while head:
                next=head.next
                head.next=pre
                pre=head
                head=next
            return pre

        # 快慢指针，快指针到达尾部，慢指针到达中间
        slow=fast=head
        while fast and fast.next:
            slow=slow.next
            fast=fast.next.next
        
        newHead=reverseList(slow)
        p1=head
        p2=newHead
        while p1 and p2:
            if p1.val != p2.val:
                return False
            p1=p1.next
            p2=p2.next
        return True
```
### 237.删除链表中的节点
    链接：https://leetcode-cn.com/problems/delete-node-in-a-linked-list/

    请编写一个函数，使其可以删除某个链表中给定的（非末尾）节点，你将只被给定要求被删除的节点。

    现有一个链表 -- head = [4,5,1,9]，它可以表示为:

    示例 1:

    输入: head = [4,5,1,9], node = 5
    输出: [4,1,9]
    解释: 给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.
    示例 2:

    输入: head = [4,5,1,9], node = 1
    输出: [4,5,9]
    解释: 给定你链表中值为 1 的第三个节点，那么在调用了你的函数之后，该链表应变为 4 -> 5 -> 9.
     

    说明:

    链表至少包含两个节点。
    链表中所有节点的值都是唯一的。
    给定的节点为非末尾节点并且一定是链表中的一个有效节点。
    不要从你的函数中返回任何结果。

题解一：
时间复杂度 O(1)
空间复杂度 O(1)
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def deleteNode(self, node):
        """
        :type node: ListNode
        :rtype: void Do not return anything, modify node in-place instead.
        """
        node.val=node.next.val
        node.next=node.next.next
```

### 328. 奇偶链表
    链接：https://leetcode-cn.com/problems/odd-even-linked-list/

    给定一个单链表，把所有的奇数节点和偶数节点分别排在一起。请注意，这里的奇数节点和偶数节点指的是节点编号的奇偶性，而不是节点的值的奇偶性。

    请尝试使用原地算法完成。你的算法的空间复杂度应为 O(1)，时间复杂度应为 O(nodes)，nodes 为节点总数。

    示例 1:

    输入: 1->2->3->4->5->NULL
    输出: 1->3->5->2->4->NULL
    示例 2:

    输入: 2->1->3->5->6->4->7->NULL 
    输出: 2->3->6->7->1->5->4->NULL
    说明:

    应当保持奇数节点和偶数节点的相对顺序。
    链表的第一个节点视为奇数节点，第二个节点视为偶数节点，以此类推。

题解一：
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def oddEvenList(self, head: ListNode) -> ListNode:
        # odd:奇数
        # even:偶数
        if not head:
            return
        odd=head
        evenHead=even=head.next
        while odd.next and even.next:
            odd.next=odd.next.next
            even.next=even.next.next
            odd,even=odd.next,even.next
        odd.next=evenHead
        return head
```

### 876.链表的中间节点
    链接：https://leetcode-cn.com/problems/middle-of-the-linked-list/

    给定一个带有头结点 head 的非空单链表，返回链表的中间结点。

    如果有两个中间结点，则返回第二个中间结点。

    示例 1：

    输入：[1,2,3,4,5]
    输出：此列表中的结点 3 (序列化形式：[3,4,5])
    返回的结点值为 3 。 (测评系统对该结点序列化表述是 [3,4,5])。
    注意，我们返回了一个 ListNode 类型的对象 ans，这样：
    ans.val = 3, ans.next.val = 4, ans.next.next.val = 5, 以及 ans.next.next.next = NULL.
    示例 2：

    输入：[1,2,3,4,5,6]
    输出：此列表中的结点 4 (序列化形式：[4,5,6])
    由于该列表有两个中间结点，值分别为 3 和 4，我们返回第二个结点。
     

    提示：给定链表的结点数介于 1 和 100 之间。

题解一|数组：
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def middleNode(self, head: ListNode) -> ListNode:
        arr=[]
        dummy=head
        while head:
            arr.append(head.val)
            head=head.next
        mid=len(arr)//2
        i=0
        while dummy:
            if i==mid:
                return dummy
            dummy=dummy.next
            i+=1
```

题解二|快慢指针：
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def middleNode(self, head: ListNode) -> ListNode:
        slow=fast=head
        while fast and fast.next:
            slow=slow.next
            fast=fast.next.next
        return slow
```

### 剑指 Offer 06. 从尾到头打印链表
    链接：https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/

    输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

    示例 1：

    输入：head = [1,3,2]
    输出：[2,3,1]
     
    限制：

    0 <= 链表长度 <= 10000

题解一：
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reversePrint(self, head: ListNode) -> List[int]:
        arr=[]
        while head:
            arr.append(head.val)
            head=head.next
        return arr[::-1]
```

### 面试题 02.01. 移除重复节点
    链接：https://leetcode-cn.com/problems/remove-duplicate-node-lcci/

    编写代码，移除未排序链表中的重复节点。保留最开始出现的节点。

    示例1:

     输入：[1, 2, 3, 3, 2, 1]
     输出：[1, 2, 3]
    示例2:

     输入：[1, 1, 1, 1, 2]
     输出：[1, 2]
    提示：

    链表长度在[0, 20000]范围内。
    链表元素在[0, 20000]范围内。
    进阶：

    如果不得使用临时缓冲区，该怎么解决？

题解一|hash：
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def removeDuplicateNodes(self, head: ListNode) -> ListNode:
        dummy=res=ListNode(-1)
        dummy.next=head
        hash={}
        while head:
            if head.val not in hash:
                hash[head.val]=1
                res=head
            else:
                res.next=head.next
            head=head.next
        return dummy.next
```
题解二|二重循环|python运行超时：

思路：

    1.快慢双指针，第一层大循环为slow非空的循环，
    2.第二层循环为fast循环，prev的下一个始终指向fast，保证出现fast.val==slow.val时，可以有效的删除相同结点，只需要fast和prev即可完美做到
    3.slow和fast双层循环依次进行，slow和fast以及以后的依次比较，直到结束


```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def removeDuplicateNodes(self, head: ListNode) -> ListNode:
        fast=slow=head
        while slow:
            fast=slow.next
            pre=slow
            while fast:
                if fast.val == slow.val: # 注意对比的是fast和slow
                    pre.next=fast.next
                else:
                    pre=pre.next
                fast=fast.next
            slow=slow.next
        return head
```

### 面试题 02.02. 返回倒数第 k 个节点
    链接：https://leetcode-cn.com/problems/kth-node-from-end-of-list-lcci/

    实现一种算法，找出单向链表中倒数第 k 个节点。返回该节点的值。

    注意：本题相对原题稍作改动

    示例：

    输入： 1->2->3->4->5 和 k = 2
    输出： 4
    说明：

    给定的 k 保证是有效的。

题解一：
```
class Solution:
    def kthToLast(self, head: ListNode, k: int) -> int:
        res=head
        n=1
        while head.next:
            n+=1
            head=head.next
        k=n-k
        while k>0:
            k-=1
            res=res.next
        return res.val

```

```
class Solution:
    def kthToLast(self, head: ListNode, k: int) -> int:
        tmp=head
        n=0
        while head:
            head=head.next
            n+=1
        k=n-k
        while k>0:
            k-=1
            tmp=tmp.next
        return tmp.val
```
题解二|快慢指针:

    定义两个指针，快指针 fast， 慢指针 slow .
    让 fast 先向前移动 k 个位置，然后 slow 和 fast 再一起向前移动 .
    当 fast 到达链表尾部，返回 slow .

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def kthToLast(self, head: ListNode, k: int) -> int:
        fast=slow=head
        while fast:
            fast=fast.next
            if k==0:
                slow=slow.next
            else:
                k-=1
        return slow.val
                  
```

### 面试题 02.03. 删除中间节点
    链接：https://leetcode-cn.com/problems/delete-middle-node-lcci/

    实现一种算法，删除单向链表中间的某个节点（除了第一个和最后一个节点，不一定是中间节点），假定你只能访问该节点。

    示例：

    输入：单向链表a->b->c->d->e->f中的节点c
    结果：不返回任何数据，但该链表变为a->b->d->e->f

题解一：
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def deleteNode(self, node):
        """
        :type node: ListNode
        :rtype: void Do not return anything, modify node in-place instead.
        """
        node.val=node.next.val
        node.next=node.next.next
```

### 面试题 02.04. 分割链表

    链接：https://leetcode-cn.com/problems/partition-list-lcci/

    编写程序以 x 为基准分割链表，使得所有小于 x 的节点排在大于或等于 x 的节点之前。如果链表中包含 x，x 只需出现在小于 x 的元素之后(如下所示)。分割元素 x 只需处于“右半部分”即可，其不需要被置于左右两部分之间。

    示例:

    输入: head = 3->5->8->5->10->2->1, x = 5
    输出: 3->1->2->10->5->5->8
题解一：
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def partition(self, head: ListNode, x: int) -> ListNode:
        before=bf=ListNode(-1)
        after=af=ListNode(-1)
        while head:
            if head.val<x:
                before.next=head
                before=before.next
            else:
                after.next=head
                after=after.next
            head=head.next
        after.next=None
        before.next=af.next
        return bf.next
```

### 面试题 02.05. 链表求和
    链接：https://leetcode-cn.com/problems/sum-lists-lcci/

    给定两个用链表表示的整数，每个节点包含一个数位。

    这些数位是反向存放的，也就是个位排在链表首部。

    编写函数对这两个整数求和，并用链表形式返回结果。

    示例：

    输入：(7 -> 1 -> 6) + (5 -> 9 -> 2)，即617 + 295
    输出：2 -> 1 -> 9，即912
    进阶：假设这些数位是正向存放的，请再做一遍。

    示例：

    输入：(6 -> 1 -> 7) + (2 -> 9 -> 5)，即617 + 295
    输出：9 -> 1 -> 2，即912

题解一：
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        if not l1:
            return l2
        if not l2:
            return l1
        dummy=res=ListNode(0)
        flag=0
        while l1 or l2:
            sum=0
            if l1:
                sum+=l1.val
                l1=l1.next
            if l2:
                sum+=l2.val
                l2=l2.next
            tmp=(sum+flag)%10
            flag=(sum+flag)//10
            res.next=ListNode(tmp)
            res=res.next
        if flag:
            res.next=ListNode(flag)
            # res.next=ListNode(1)
        return dummy.next
```

### 面试题 02.06. 回文链表
    链接：https://leetcode-cn.com/problems/palindrome-linked-list-lcci/

    编写一个函数，检查输入的链表是否是回文的。

    示例 1：

    输入： 1->2
    输出： false 
    示例 2：

    输入： 1->2->2->1
    输出： true 
     

    进阶：
    你能否用 O(n) 时间复杂度和 O(1) 空间复杂度解决此题？

题解一|数组：
```
class Solution:
    def isPalindrome(self, head: ListNode) -> bool:
        arr=[]
        while head:
            arr.append(head.val)
            head=head.next
        return arr==arr[::-1]
```
题解二|快慢指针:
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def isPalindrome(self, head: ListNode) -> bool:
        if not head:
            return True
        slow=fast=head
        while fast and fast.next:
            slow=slow.next
            fast=fast.next.next
        pre=slow
        while slow and slow.next:
            next=slow.next.next
            slow.next.next=pre
            pre=slow.next
            slow.next=next
        while head and pre:
            if head.val != pre.val:
                return False
            head=head.next
            pre=pre.next
        return True
```

### 面试题 02.07. 链表相交
    链接：https://leetcode-cn.com/problems/intersection-of-two-linked-lists-lcci/

    给定两个（单向）链表，判定它们是否相交并返回交点。请注意相交的定义基于节点的引用，而不是基于节点的值。换句话说，如果一个链表的第k个节点与另一个链表的第j个节点是同一节点（引用完全相同），则这两个链表相交。


    示例 1：

    输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
    输出：Reference of the node with value = 8
    输入解释：相交节点的值为 8 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。

    示例 2：

    输入：intersectVal = 2, listA = [0,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
    输出：Reference of the node with value = 2
    输入解释：相交节点的值为 2 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [0,9,1,2,4]，链表 B 为 [3,2,4]。在 A 中，相交节点前有 3 个节点；在 B 中，相交节点前有 1 个节点。

    示例 3：

    输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
    输出：null
    输入解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
    解释：这两个链表不相交，因此返回 null。

    注意：

    如果两个链表没有交点，返回 null 。
    在返回结果后，两个链表仍须保持原有的结构。
    可假定整个链表结构中没有循环。
    程序尽量满足 O(n) 时间复杂度，且仅用 O(1) 内存。

题解一|循环|超时：

```
class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        p,q=headA.next,headB.next
        while p:
            while q:
                if p==q:
                    return q
            q=q.next
        p=p.next
        return None
```
题解二|双指针：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/link10.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/link10.png)

算法让两个指针分别从A和B点往C点走，两个指针分别走到C后，又各自从另外一个指针的起点，也就是A指针第二次走从B点开始走，B指针同理，这样，A指针走的路径长度 AO + OC + BO 必定等于B指针走的路径长度 BO + OC + AO，这也就意味着这两个指针第二轮走必定会在O点相遇，相遇后也即到达了退出循环的条件。

```
class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        p,q=headA,headB
        while p != q:
            if p:
                p=p.next
            else:
                p=headB
            if q:
                q=q.next
            else:
                q=headA
        return p
```

### 面试题 02.08. 环路检测
    链接：https://leetcode-cn.com/problems/linked-list-cycle-lcci/

    给定一个有环链表，实现一个算法返回环路的开头节点。
    有环链表的定义：在链表中某个节点的next元素指向在它前面出现过的节点，则表明该链表存在环路。

    示例 1：

    输入：head = [3,2,0,-4], pos = 1
    输出：tail connects to node index 1
    解释：链表中有一个环，其尾部连接到第二个节点。

    示例 2：

    输入：head = [1,2], pos = 0
    输出：tail connects to node index 0
    解释：链表中有一个环，其尾部连接到第一个节点。

    示例 3：

    输入：head = [1], pos = -1
    输出：no cycle
    解释：链表中没有环。

    进阶：你是否可以不用额外空间解决此题？

题解一|双指针：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/linknode6.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/linknode6.png)

z=n*环的大小+z

```
class Solution:
    def detectCycle(self, head: ListNode) -> ListNode:
        if not head:
            return None
        slow=fast=head
        while  fast and fast.next:
            slow=slow.next
            fast=fast.next.next
            if slow==fast:
                slow=head
                while slow!=fast:
                    slow=slow.next
                    fast=fast.next
                return slow
        return None
```

```
class Solution:
    def detectCycle(self, head: ListNode) -> ListNode:
        if not head:
            return None
        slow=fast=head
        while True:
            if not (fast and fast.next):
                return
            slow=slow.next
            fast=fast.next.next
            if slow == fast:
                break
        slow=head
        while slow != fast:
            slow=slow.next
            fast=fast.next
        return slow  
```

### 面试题18. 删除链表的节点
    链接：https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/

    给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。

    返回删除后的链表的头节点。

    注意：此题对比原题有改动

    示例 1:

    输入: head = [4,5,1,9], val = 5
    输出: [4,1,9]
    解释: 给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.
    示例 2:

    输入: head = [4,5,1,9], val = 1
    输出: [4,5,9]
    解释: 给定你链表中值为 1 的第三个节点，那么在调用了你的函数之后，该链表应变为 4 -> 5 -> 9.
     

    说明：

    题目保证链表中节点的值互不相同
    若使用 C 或 C++ 语言，你不需要 free 或 delete 被删除的节点

题解一：
```
class Solution:
    def deleteNode(self, head: ListNode, val: int) -> ListNode:
        dummy=ListNode(-1)
        dummy.next=head
        res=dummy
        while res.next:
            if res.next.val==val:
                res.next=res.next.next
            else:
                res=res.next
        return dummy.next
```

### 面试题22. 链表中倒数第k个节点
    链接：https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/

    个链表的倒数第3个节点是值为4的节点。

    示例：

    给定一个链表: 1->2->3->4->5, 和 k = 2.

    返回链表 4->5.

题解一：
```
class Solution:
    def getKthFromEnd(self, head: ListNode, k: int) -> ListNode:
        fast=slow=head
        while fast:
            fast=fast.next
            if k==0:
                slow=slow.next
            else:
                k-=1
        return slow
```

### 面试题24. 反转链表
    链接：https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/

    定义一个函数，输入一个链表的头节点，反转该链表并输出反转后链表的头节点。

    示例:

    输入: 1->2->3->4->5->NULL
    输出: 5->4->3->2->1->NULL
     

    限制：

    0 <= 节点个数 <= 5000

题解一：
```
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        pre=None
        while head:
            next=head.next
            head.next=pre
            pre=head
            head=next
        return pre
```

### 面试题35. 复杂链表的复制

    链接：https://leetcode-cn.com/problems/fu-za-lian-biao-de-fu-zhi-lcof/

    请实现 copyRandomList 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 next 指针指向下一个节点，还有一个 random 指针指向链表中的任意节点或者 null。

题解一：
```
class Solution:
    def copyRandomList(self, head: 'Node') -> 'Node':
        return copy.deepcopy(head)
```

题解二|深度优先遍历：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, x: int, next: 'Node' = None, random: 'Node' = None):
        self.val = int(x)
        self.next = next
        self.random = random
"""
class Solution:
    def copyRandomList(self, head: 'Node') -> 'Node':
        def dfs(head):
            if not head:
                return None
            if head in visited:
                return visited[head]
            copy=Node(head.val,None,None)
            visited[head]=copy
            copy.next=dfs(head.next)
            copy.random=dfs(head.random)
            return copy
        visited={}
        return dfs(head)
```

题解三|bfs：
```
"""
# Definition for a Node.
class Node:
    def __init__(self, x: int, next: 'Node' = None, random: 'Node' = None):
        self.val = int(x)
        self.next = next
        self.random = random
"""
import collections

class Solution:
    def copyRandomList(self, head: 'Node') -> 'Node':
        visited={}
        def bfs(head):
            if not head:
                return None
            clone=Node(head.val,None,None)
            queue=collections.deque()
            queue.append(head)
            visited[head]=clone
            while queue:
                tmp=queue.pop()
                if tmp.next and tmp.next not in visited:
                    visited[tmp.next]=Node(tmp.next.val,[],[])
                    queue.append(tmp.next)
                if tmp.random and tmp.random not in visited:
                    visited[tmp.random] = Node(tmp.random.val, [], [])
                    queue.append(tmp.random)
                visited[tmp].next=visited.get(tmp.next)
                visited[tmp].random=visited.get(tmp.random)
            return clone
        return bfs(head)
```

### 面试题52. 两个链表的第一个公共节点
    链接：https://leetcode-cn.com/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/

    输入两个链表，找出它们的第一个公共节点。

    如下面的两个链表：

    在节点 c1 开始相交。

    示例 1：

    输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
    输出：Reference of the node with value = 8
    输入解释：相交节点的值为 8 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。

    示例 2：

    输入：intersectVal = 2, listA = [0,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
    输出：Reference of the node with value = 2
    输入解释：相交节点的值为 2 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [0,9,1,2,4]，链表 B 为 [3,2,4]。在 A 中，相交节点前有 3 个节点；在 B 中，相交节点前有 1 个节点。
 
    示例 3：

    输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
    输出：null
    输入解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
    解释：这两个链表不相交，因此返回 null。
 
    注意：

    如果两个链表没有交点，返回 null.
    在返回结果后，两个链表仍须保持原有的结构。
    可假定整个链表结构中没有循环。
    程序尽量满足 O(n) 时间复杂度，且仅用 O(1) 内存。

题解一|hash：
```
class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        p=headA
        q=headB
        hash={}
        while headA:
            if headA not in hash:
                hash[headA]=1
                headA=headA.next
        while headB:
            if headB in hash:
                return headB
            else:
                headB=headB.next
        return None
```

题解二|双指针：

时间复杂度：O(M+N)
空间复杂度：O(1)
```
class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        node1, node2 = headA, headB
        
        while node1 != node2:
            node1 = node1.next if node1 else headB
            node2 = node2.next if node2 else headA

        return node1
```

