---
title: 数据结构与算法-1-KMP算法
date: 2020-04-27 20:32:17
tags: 数据结构与算法
categories:
        - 数据结构与算法
---
参考：
    http://www.ruanyifeng.com/blog/2013/05/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm.html
    https://www.bilibili.com/video/BV1jb411V78H?from=search&seid=3722651774629068033
    https://www.bilibili.com/video/BV1Px411z7Yo?from=search&seid=3722651774629068033
    https://www.bilibili.com/video/BV1hW411a7ys?from=search&seid=3722651774629068033
    https://www.cnblogs.com/dahu-daqing/p/9302668.html

## 一、KMP

KMP算法，又称模式匹配算法，能够在线性时间内判定字符串 T 是否为 S 的子串，并求出字符串 T 在 S 中各次出现的位置。

## 中心扩散

    思路：

        每个字母当成回文串的中心
        考虑两种情况:回文串的长度为奇数或者偶数情况。

    缺点：
        奇数和偶数需要分开讨论
        没有充分利用前面查找的结果
        没有思考回文字符本身的特性--对称性


```
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n=len(s)
        self.res=''
        def helper(i,j):
            while i>= 0 and j<n and s[i]==s[j]:
                i-=1
                j+=1
            if len(self.res) < j-i-1:
                self.res=s[i+1:j]
                # print(i,self.res)
        
        for i in range(n):
            helper(i,i)
            helper(i,i+1) # 解决case为"cbbd",即解决回文串为偶数的情况
        return self.res
```

## 二、代码
    1、解决奇偶数问题
        在原字符串的每个相邻两个字符中间插入一个分隔符，同时在首尾也添加分隔符，如何#
    2、字符串观察
        s:aaaba
        t:#a#a#a#b#a#
        回文半径数组：RL

        RL[i]-1等于回文串的长度


![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/manacher.jpg](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/manacher.jpg)

时间复杂度：O(m+n)

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# @contact: voidqueens@hotmail.com
# @software: PyCharm
# @time: 2020/4/26 下午6:50
# @site: www.gongyanli.com
# @file: kmp.py

def getNext(p):
    '''
    p为模式串
    返回next数组，即部分匹配表
    等同于从模式字符串的第1位(注意，不包括第0位)开始对自身进行匹配运算。
    :param p:
    :return:
    '''
    next = [0] * len(p)
    next[0] = -1
    i = 0
    j = -1
    while i < len(p) - 1:
        if j == -1 or p[i] == p[j]: # i遍历的是数组下标，j则为next赋值
            i += 1
            j += 1
            next[i] = j
        else:
            j = next[j]
    return next


def kmp(s, p):
    '''
    s为主串
    p为模式串
    如果t里有p，返回打头下标
    :param s:
    :param p:
    :return:
    '''
    next = getNext(p)
    i = j = 0
    while i < len(s) and j < len(p):
        if j == -1 or s[i] == p[j]: 
            i += 1
            j += 1
        else:
            j = next[j]
    if j == len(p):
        return i - j
    else:
        return -1


s = 'ababababca'
p = 'abababca'
print(getNext(p))
print(kmp(s, p))

```
