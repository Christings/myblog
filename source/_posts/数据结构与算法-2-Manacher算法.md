---
title: 数据结构与算法-2-Manacher算法
date: 2020-04-29 11:49:54
tags: 数据结构与算法
categories:
        - 数据结构与算法
---
参考：https://segmentfault.com/a/1190000003914228

## 一、Manacher
    Manacher用来在字符串中求最长回文串。

## 二、代码
```
def manacher(s):
    s='#'+'#'.join(s)+'#'

    RL=[0]*len(s)
    maxRight=0
    pos=0
    maxLen=0

    for i in range(len(s)):
        if i<MaxRight:
            RL[i]=min(RL[2*pos-i], MaxRight-i)
        else:
            RL[i]=1
        #尝试扩展，注意处理边界
        while i-RL[i]>=0 and i+RL[i]<len(s) and s[i-RL[i]]==s[i+RL[i]]:
            RL[i]+=1
        #更新MaxRight,pos
        if RL[i]+i-1>MaxRight:
            MaxRight=RL[i]+i-1
            pos=i
        #更新最长回文串的长度
        maxLen=max(maxLen, RL[i])
    return maxLen-1
```
