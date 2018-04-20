---
title: 用Python实现单链表逆序
date: 2018-04-02 13:54:06
tags: [数据结构]
categories:
		- 数据结构
		- 链表
---
### 一、原理
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
### 二、用Python实现单链表逆序
参考：[https://leetcode.com/problems/reverse-linked-list/solution/](https://leetcode.com/problems/reverse-linked-list/solution/)  
	 [http://baijiahao.baidu.com/s?id=1574261186522385&wfr=spider&for=pc](http://baijiahao.baidu.com/s?id=1574261186522385&wfr=spider&for=pc)  
	 [https://blog.csdn.net/autumn20080101/article/details/7607148](https://blog.csdn.net/autumn20080101/article/details/7607148)  
	 [https://www.cnblogs.com/scud001/p/4423324.html](https://www.cnblogs.com/scud001/p/4423324.html)

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
	            next = head.next
	            head.next = prve
	            prve = head
	            head = next
	        return prve