---
title: LeetCode-7-递归
date: 2020-01-16 18:30:44
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
## 七.递归

参考文章：https://mp.weixin.qq.com/s?__biz=Mzg2NzA4MTkxNQ==&mid=2247485092&idx=1&sn=ff44f169aa57cac8a8e6595b7ff49ab8&scene=21#wechat_redirect

递归的三大要素:
    
    第一要素：明确你这个函数想要干什么
    第二要素：寻找递归结束条件
    第三要素：找出函数的等价关系式,不断缩小参数的范围

递归优化（建议看完一题再看优化）：

    1.考虑是否重复计算
    比如说f(n-1),f(n-2)等就是 f(n) 的子问题了，这些子问题会被重复计算。

    一般我们可以把我们计算的结果保证起来，例如把 f(4) 的计算结果保证起来，当再次要计算 f(4) 的时候，我们先判断一下，之前是否计算过，如果计算过，直接把 f(4) 的结果取出来就可以了，没有计算过的话，再递归计算。

```
def fib(n):
        if n<=2:
            return n

        # 判断是否计算过
        if arr[n] != -1:
            return arr[n]
        else:
            # 没有计算过，递归计算,并且把结果保存到 arr数组里
            arr[n]=f(n-1)+f(n-2)
            return arr[n]
```

    2.考虑是否可以自底向上,这种方法也被称为递推。
    
    对于递归的问题，我们一般都是从上往下递归的，直到递归到最底，再一层一层着把值返回。
    不过，有时候当 n 比较大的时候，例如当 n = 10000 时，那么必须要往下递归10000层直到 n <=1 才将结果慢慢返回，如果n太大的话，可能栈空间会不够用。

```
def f(n):
        if n<=2:
            return n
        f1=1
        f2=2
        sum=0
        for i in range(3,n+1):
            sum=f1+f2
            f1=f2
            f2=sum
        return  sum
```

### 在两个长度相等的排序数组中找到上中位数

    给定两个有序数组arr1和arr2，已知两个数组的长度都为N，求两个数组中所有数的上中位数。要求时间复杂度O(logN)，空间复杂度O(1）

    例如 arr1 = [1, 2,3,4]，arr2 = [3,4,5,6]。

    总共8个数，则中位数就是第 4 小的数，为 3.

    例如 arr1 = [0,1,2]，arr2 = [3,4,5]。

    总共6个数，则中位数就是第 3 小的数，为 2.

参考文章：https://mp.weixin.qq.com/s?__biz=Mzg2NzA4MTkxNQ==&mid=2247485087&idx=3&sn=9db76b899a0ed1c9274211a0218c2e8e&scene=21#wechat_redirect

题解一（递归）：

    (1)、当 两个数组的长度为偶数时：
    
    mid1 = (n-1)/2 = 1。
    mid2 = (n - 1)/2 = 1。

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/median1.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/median1.png)

    (2)、当两个数组的长度为奇数时：
    
    mid1 = (n-1)/2 = 2。
    mid2 = (n - 1)/2 = 2。

    如果 arr2[mid2] > arr1[mid1] 时，则和上面那个情况有点小差别，这时候目标数只存在于 arr1[mid1…n] 和 arr2[0…mid2]中。注意他们的差别，从arr1[mid1+1…n] => arr1[mid1…n]。

    (3)、总结

    当数组长度为奇数时：
        arr2[mid1] < arr1[mid2]：mid1+1~n和0～mid2
        arr2[mid1] > arr1[mid2]：0～mid1和mid2+1～n
    当数组长度为偶数时:
        arr2[mid1] < arr1[mid2]：mid1~n和0～mid2
        arr2[mid1] > arr1[mid2]：0～mid1和mid2～n

```
def getUpMedian(arr1, arr2):
    if arr1 is None or arr2 is None:
        return -1

    return helper(arr1, 0, len(arr1) - 1, arr2, 0, len(arr2) - 1)


def helper(arr1, l1, r1, arr2, l2, r2):
    # mid1 = (r1 + l1) // 2
    # mid2 = (r2 + l2) // 2

    mid1 = l1+(r1 - l1) // 2 # 不明白为何这样求中位数？
    mid2 = l2+(r2 - l2) // 2

    if l1 >= r1:
        return min(arr1[l1], arr2[l2])

    offset = ((r1 - l1 + 1) & 1) ^ 1

    if arr1[mid1] < arr2[mid2]:
        return helper(arr1, mid1 + offset, r1, arr2, l2, mid2)
    elif arr1[mid1] > arr2[mid2]:
        return helper(arr1, l1, mid1, arr2, mid2 + offset, r2)
    else:
        return arr1[mid1]

print(getUpMedian([1, 2, 4, 6,9], [2, 2, 7, 9,10]))
```

题解二（迭代）：
```
def getUpMedian(arr1, arr2):
    if arr1 is None or arr2 is None:
        return -1

    l1 = 0
    r1 = len(arr1) - 1
    l2 = 0
    r2 = len(arr2) - 1
    mid1, mid2 = 0, 0
    offset = 0

    while l1 < r1:
        # mid1 = (r1 - l1) // 2
        # mid2 = (r2 - l2) // 2
        mid1 = l1 + (r1 - l1) // 2
        mid2 = l2 + (r2 - l2) // 2
        offset = ((r1 - l1 + 1) & 1) ^ 1

        if arr1[mid1] < arr2[mid2]:
            l1 = mid1 + offset
            r2 = mid2
        elif arr1[mid1] > arr2[mid2]:
            r1 = mid1
            l2 = mid2 + offset
        else:
            return arr1[mid1]
    return min(arr1[l1], arr2[l2])

print(getUpMedian([1, 2, 4, 6, 9], [2, 2, 7, 9, 10]))
```

### ？？？求两个有序数组的第K小数

    给定两个有序数组arr1和arr2，已知两个数组的长度分别为 m1 和 m2，求两个数组中的第 K 小数。要求时间复杂度O(log(m1 + m2))。

    【举例】
    例如 arr1 = [1, 2,3]，arr2 = [3,4,5,6]，K = 4。

    则第 K 小数为 3.

    例如 arr1 = [0,1,2]，arr2 = [3,4,5，7，8]， K = 3;

    则第 K 小数为 2.

https://mp.weixin.qq.com/s?__biz=Mzg2NzA4MTkxNQ==&mid=2247485084&idx=3&sn=2cd46fef0f9e80bfe5a47043afc8ccbb&scene=21#wechat_redirect

题解一（递归）：

```
def findKth(arr1, arr2, k):
    if arr1 is None or len(arr1) < 1:
        return arr2[k - 1]
    if arr2 is None or len(arr2) < 1:
        return arr1[k - 1]
    return helper(arr1, 0, len(arr1) - 1, arr2, 0, len(arr2) - 1, k)


def helper(arr1, l1, r1, arr2, l2, r2, k):
    if l1 > r1:
        return arr2[l2 + k]
    if l2 > r2:
        return arr1[l1 + k]
    if k == 0:
        return min(arr1[l1], arr2[l2])

    mid1 = l1 + k // 2 if (l1 + k // 2) < (r1) else r1
    mid2 = l2 + k // 2 if (l1 + k // 2) < (r2 - l1) else r2

    if arr1[mid1] < arr2[mid2]:
        return helper(arr1, mid1 + 1, r1, arr2, l2, r2, k - k // 2 - 1)
    elif arr1[mid1] > arr2[mid2]:
        return helper(arr1, l1, r1, arr2, mid2 + 1, r2, k - k // 2 - 1)
    else:
        return arr1[mid1]

```

### 70.爬楼梯
    链接：https://leetcode-cn.com/problems/climbing-stairs/

    假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

    每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

    注意：给定 n 是一个正整数。

    示例 1：

    输入： 2
    输出： 2
    解释： 有两种方法可以爬到楼顶。
    1.  1 阶 + 1 阶
    2.  2 阶
    示例 2：

    输入： 3
    输出： 3
    解释： 有三种方法可以爬到楼顶。
    1.  1 阶 + 1 阶 + 1 阶
    2.  1 阶 + 2 阶
    3.  2 阶 + 1 阶

题解一(递归):

    第一要素：明确你这个函数想要干什么
```
    f(n) 的功能是跳上一个n级的台阶总共有多少种跳法.
    def f(n):
        pass
```

    第二要素：寻找递归结束条件
```
    n=1,f(1) = 1
    n=2,f(2) = 2

    def f(n):
        if n<= 2:
            return n
```
    第三要素：找出函数的等价关系式,不断缩小参数的范围

```
    每次跳的时候，小青蛙可以跳一个台阶，也可以跳两个台阶，也就是说，每次跳的时候，小青蛙有两种跳法。

    跳上n个台阶时，可以从n-1台阶跳上来，或者从n-2台阶跳上来。

    第一种跳法：第一次我跳了一个台阶，那么还剩下n-1个台阶还没跳，剩下的n-1个台阶的跳法有f(n-1)种。

    第二种跳法：第一次跳了两个台阶，那么还剩下n-2个台阶还没，剩下的n-2个台阶的跳法有f(n-2)种。

    f(n) = f(n-1) + f(n-2)

    def f(n):
        # 递归结束条件
        if n<=2:
            return n
        # 等价关系式
        return  fib(n-1)+fib(n-2)
```
### 206. 反转链表
    链接：https://leetcode-cn.com/problems/reverse-linked-list/

    反转一个单链表。

    示例:

    输入: 1->2->3->4->5->NULL
    输出: 5->4->3->2->1->NULL
    进阶:
    你可以迭代或递归地反转链表。你能否用两种方法解决这道题？

题解一（递归）:

    第一要素：明确你这个函数想要干什么
```
    reverseList(head) 的功能是反转单链表，head表示头节点
    def reverseList(head):
        pass
```

    第二要素：寻找递归结束条件
    当链表只有一个节点，或者链表为空时，递归结束。
```
    def reverseList(head):
        if head is None or head.next is None:
            return head
```

    第三要素：找出函数的等价关系式,不断缩小参数的范围

    等价条件中，一定是范围不断在缩小，对于链表来说，就是链表的节点个数不断在变小.
    head -> 1 -> 2 ->3 ->4

    newList -> 4 -> 3 -> 2 <- 1 <- head

    接下来只需要把节点 2 的 next 指向 1，然后把 1 的 next 指向 null,不就行了.

    newList -> 4 -> 3 -> 2 -> 1 -> null

```
    def reverseList(head):
        def helper(head):
            if head is None or head.next is None:
                return head
            pre,last=helper(head.next)
            last.next=head
            head.next=None
            return pre,head
        rt,_=helper(head)
        return rt
```

```
不理解？？？
class Solution(object):
    def reverseList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        # 递归终止条件是当前为空，或者下一个节点为空
        if(head==None or head.next==None):
            return head
        # 这里的cur就是最后一个节点
        cur = self.reverseList(head.next)
        # 这里请配合动画演示理解
        # 如果链表是 1->2->3->4->5，那么此时的cur就是5
        # 而head是4，head的下一个是5，下下一个是空
        # 所以head.next.next 就是5->4
        head.next.next = head
        # 防止链表循环，需要将head.next设置为空
        head.next = None
        # 每层递归函数都返回cur，也就是最后一个节点
        return cur
```
### 509.斐波那契数列
    链接：https://leetcode-cn.com/problems/fibonacci-number/

    斐波那契数，通常用 F(n) 表示，形成的序列称为斐波那契数列。该数列由 0 和 1 开始，后面的每一项数字都是前面两项数字的和。也就是：

    F(0) = 0,   F(1) = 1
    F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
    给定 N，计算 F(N)。

     

    示例 1：

    输入：2
    输出：1
    解释：F(2) = F(1) + F(0) = 1 + 0 = 1.
    示例 2：

    输入：3
    输出：2
    解释：F(3) = F(2) + F(1) = 1 + 1 = 2.
    示例 3：

    输入：4
    输出：3
    解释：F(4) = F(3) + F(2) = 2 + 1 = 3.
     

    提示：

    0 ≤ N ≤ 30

题解一（递归）:

    第一要素：明确你这个函数想要干什么
```
    f(n) 的功能是求第 n 项的值.

    def fib(n):
        pass
```
    第二要素：寻找递归结束条件
```
    f(1) = f(2) = 1。所以递归结束条件可以为  n <= 2。

    def fib(n):
        if n<2:
            return 1
```
    第三要素：找出函数的等价关系式,不断缩小参数的范围

```
    f(n) = f(n-1) + f(n-2)

    def fib(n):
        # 递归结束条件
        if n<2:
            return 1
        # 等价关系式
        return  fib(n-1)+fib(n-2)
```

### 969. 煎饼排序
    链接：https://leetcode-cn.com/problems/pancake-sorting/

    给定数组 A，我们可以对其进行煎饼翻转：我们选择一些正整数 k <= A.length，然后反转 A 的前 k 个元素的顺序。我们要执行零次或多次煎饼翻转（按顺序一次接一次地进行）以完成对数组 A 的排序。

    返回能使 A 排序的煎饼翻转操作所对应的 k 值序列。任何将数组排序且翻转次数在 10 * A.length 范围内的有效答案都将被判断为正确。

    示例 1：

    输入：[3,2,4,1]
    输出：[4,2,4,3]
    解释：
    我们执行 4 次煎饼翻转，k 值分别为 4，2，4，和 3。
    初始状态 A = [3, 2, 4, 1]
    第一次翻转后 (k=4): A = [1, 4, 2, 3]
    第二次翻转后 (k=2): A = [4, 1, 2, 3]
    第三次翻转后 (k=4): A = [3, 2, 1, 4]
    第四次翻转后 (k=3): A = [1, 2, 3, 4]，此时已完成排序。 
    示例 2：

    输入：[1,2,3]
    输出：[]
    解释：
    输入已经排序，因此不需要翻转任何内容。
    请注意，其他可能的答案，如[3，3]，也将被接受。
 
    提示：

    1 <= A.length <= 100
    A[i] 是 [1, 2, ..., A.length] 的排列

题解一|递归

思路：

    1、找到n个饼中最大的那个。

    2、把这个最大的饼移到最底下。

    3、递归调用pancakeSort(A, n - 1)。

    base case：n == 1时，排序 1 个饼时不需要翻转。

操作：

1、用锅铲将前 3 块饼翻转一下，这样最大的饼就翻到了最上面。

2、用锅铲将前n块饼全部翻转，这样最大的饼就翻到了第n块，也就是最后一块。

时间复杂度：因为递归调用的次数是n，每次递归调用都需要一次 for 循环，时间复杂度是 O(n)，所以总的复杂度是 O(n^2)。

```
class Solution:
    def pancakeSort(self, A: List[int]) -> List[int]:
        self.res=[]
        self.sort(A,len(A))
        return self.res
    
    def sort(self,arr,n):
        if n==1:
            return
        maxCake=0
        maxCakeIndex=0
        # 寻找最大饼的索引
        for i in range(n):
            if arr[i]>maxCake:
                maxCake=arr[i]
                maxCakeIndex=i
        self.reverse(arr,0,maxCakeIndex) # 第一次翻转，将最大饼翻到最上面
        self.res.append(maxCakeIndex+1)
        self.reverse(arr,0,n-1) # 第二次翻转，将最大饼翻到最下面
        self.res.append(n)

        self.sort(arr,n-1) # 递归调用

    def reverse(self,arr,i,j):
        while i<j:
            arr[i],arr[j]=arr[j],arr[i]
            i+=1
            j-=1
```