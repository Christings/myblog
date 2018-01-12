---
title: nltk-一-入门
date: 2018-01-12 10:13:56
tags: nltk 
categories:
		- 聊天机器人
		- nltk
---
>本文首发于我的博客：
>
>参考博客：http://www.shareditor.com/blogshow?blogId=65
## 一、NTLK库安装
	$ pip install nltk
## 二、下载书籍
	$ python
	>>> import nltk
	>>> nltk.download()
	选择语料库--book进行下载
	>>> nltk.book import *
	>>> text1
	<Text: Moby Dick by Herman Melville 1851>
## 三、NLTK的使用


	1.搜索文本
		>>> text1.concordancd("next")
		> 显示20个包含next的语句上下文
	2.搜索相关词
		>>> text1.similar("boat")
		>查找出boat的近义词等相关词
	3.查找词在文章中的位置
		>>> text1.dispersion_plot(["bext","city","America"])
		> 如果出现错误，请记得安装matplotlib
		> pip install matplotlib
	4.词统计
		len(text1)：返回总字数
		set(text1)：返回文本的所有词集合
		len(set(text4))：返回文本总词数
		text4.count("is")：返回“is”这个词出现的总次数
		FreqDist(text1)：统计文章的词频并按从大到小排序存到一个列表里
		fdist1 = FreqDist(text1);fdist1.plot(50, cumulative=True)：统计词频，并输出累计图像
		fdist1.hapaxes()：返回只出现一次的词text4.collocations()：频繁的双联词

 
	