---
title: nltk-2-语料库使用
date: 2018-01-12 11:37:01
tags: [nltk]
categories:
		- 机器学习和深度学习
		- nltk
---
## NLTK语料库

	from nltk.corpus import webtext：网络文本语料库，网络和聊天文本

	from nltk.corpus import brown：布朗语料库，按照文本分类好的500个不同来源的文本

	from nltk.corpus import reuters：路透社语料库，1万多个新闻文档

	from nltk.corpus import inaugural：就职演说语料库，55个总统的演说

	rom nltk.corpus import gutenberg：大约有36000本免费的电子图书

## 语料库通用接口

	fileids()：返回语料库中的文件

	categories()：返回语料库中的分类

	raw()：返回语料库的原始内容

	words()：返回语料库中的词汇

	sents()：返回语料库句子

	abspath()：指定文件在磁盘上的位置

	open()：打开语料库的文件流

# 加载自己本地的语料库

	`from nltk.corpus import PlaintextCorpusReader
	corpus_root = '/tmp'
	wordlists = PlaintextCorpusReader(corpus_root, '.*')
	wordlists.fileids()`