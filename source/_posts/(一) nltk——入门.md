---
title: nltk-一-入门
date: 2018-01-12 10:13:56
tags: nltk 
categories:
		- nlp
		- nltk
---
>本文首发于我的博客：[gongyanli.com](http://gongyanli.com/(%E4%B8%80)%20nltk%E2%80%94%E2%80%94%E5%85%A5%E9%97%A8/)  
>
>参考博客：http://www.shareditor.com/blogshow?blogId=65  

前言：NLTK(Natural Language Toolkit) 是一个用于构建处理人类语言数据的python应用的开源平台。它提供了超过50多个素材库和词库资源的易用接口，其中有很多文本处理库，可以用来给文本分类，符号化，提取词根，贴标签，解析，语义推理，或者打包成工业级的自然语言处理库。本文简单介绍了nltk的安装与使用，希望对小伙伴们有用。

## 一、NTLK库安装
	$ pip install nltk
## 二、NLTK模块与功能介绍
	
	语言处理任务			NLTK模块				功能描述
	获取语料库			nltk.corpus			语料库和词典的标准化接口
	字符串处理			nltk.tokenize,		分词、句子分解
						nltk.stem			词干化
	搭配研究				nltk.collocations	t-检验、卡方、点互信息
	词性标注				nltk.tag			n-gram、backoff、Brill、HMM、TnT
	分类					nltk.classify		决策树、最大熵、朴素贝叶斯、EM、K-Means
						nltk.cluster		
	分块					nltk.chunk			正则表达式、n-gram、命名实体
	解析					nltk.parse			图标、基于特征、一致性、概率性、依赖项
	语义解释				nltk.stem			λ演算、一阶逻辑、模型检验
						nltk.inference		
	指标测评				nltk.metrics		精度、召回率、协议系数
	概率与估计			nltk.probalility	频率分布、平滑概率分布
	应用					nltk.app			图形化的关键词排序、分析器、wordnet查看器
						nltk.chat
	语言学领域的工作		nltk.toolbox		处理STL工具箱格式的数据
	
## 三、下载书籍
	$ python
	>>> import nltk
	>>> nltk.download()
	选择语料库--book进行下载
	>>> nltk.book import *
	>>> text1
	<Text: Moby Dick by Herman Melville 1851>
## 四、NLTK基本使用

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
## 五、NLTK--文本预处理
1.分词
(1)英文分词

	方法1：将文本分割成单词
	split()
	方法2：将文本分割成句子
	from nltk.tokenize import sent_tokenize
	sent=sent_token(text)
	方法3:将文本分割成单词
	from nltk.tokenize import word_tokenize
	word=word_tokenize(text)
(2)非英文分词

	from nltk.tokenize import sent_tokenize
	sent=sent_token(text,"french")
	
2.词干化处理（stem）

文档分词后一般要进行词干化处理，词干化处理就是将名词复数变为单数，动词不同时态变为基本时态等。

	from nltk.stem import PorterStemmer
	from nltk.corpus import wordnet as wn
	from nltk import PorterStemmer
	from nltk.stem.lancaster import LancasterStemmer
	
	def stemWords(self,wordsList):
		
		# 方法1
		porter=PorterStemmer()
		result=[porter.stem(w) for w in wordsList]
		# 方法2
		stemWords=[]
		for words in wordsList:
			stemWords+=[[wn.morphy(w) for w in words]]
		return stemWords
		# 方法3--支持非英文词干化提取
		stemmer=SnowballStemmer("english")
		result=stemmer.stem("imaging")
		# 方法4
		lancaster_stemmer = LancasterStemmer()
		lancaster_stemmer.stem(‘saying’)
		
3.单词变体还原--将单词还原成一个真实的单词
当用PorterStemmer和PorterStemmer对imaging这个单词进行词干化处理时，得到的结果是imag，显然这并不是我们想要的结果，所以这时候就很有必要进行还原。
有时候将单词变体还原时，总是得到同样的词，这是因为语言默认部分为名词，要想得到动词，使用pos="v"

	from nltk.stem import WordNetLemmatizer
	lemmatizer = WordNetLemmatizer()
	print(lemmatizer.lemmatize('imaging', pos="v"))

	pos="v"--动词
	pos="n"--名词
	pos="a"--形容词
	pos="r"--副词

4.停用词处理（stopwords）
	
	from nltk.corpus import stopwordds
	stopwordset=stopwords.words("english")
	clean_tokens=list()
	for token in tokens:
		if token not in stopwordset:
			clean_tokens.append(token)
	# clean_list=[token for tokens in text if token not in stopwordset]
5.同义词处理
	
	# WordNet是一个为自然语言处理而建立的数据库，包含一些同义词组及简短定义
	from nltk.corpus import wordnet
	# 获取单词的定义和示例
  	syn = wordnet.synsets("pain")
	print(syn[0].definition())
	print(syn[0].examples())
	# 获取同义词
	synonyms = []
	for syn in wordnet.synsets("computer"):
		for lemma in syn.lemmas():
			synonyms.append(lemma.name())
	print(synonyms)
6.反义词处理
	
	from nltk.corpus import wordnet
  	antonyms = []
	for syn in wordnet.synsets("small"):
    	for l in syn.lemmas():
        	if l.antonyms():
            	antonyms.append(l.antonyms()[0].name())
	print(antonyms)
7.词性标注(pos tagger)
	
	import nltk
	tokens=nltk.pos_tag(tokens) # 进行词性标注
	
	# 查看相关说明文档
	nltk.help.upeen_tagset("JJ")
	nltk.help.upeen_tagset("IN")
	nltk.help.upeen_tagset("NNP")
8.去掉数字标点和非字母字符

	def CleanLines(self,line):  
	    identify = string.maketrans('', '')  
	    delEStr = string.punctuation +string.digits  #ASCII 标点符号，数字    
	    cleanLine =line.translate(identify,delEStr) #去掉ASCII 标点符号  
	   return cleanLine  
9.拼写检查

	def WordCheck(self,words):#拼写检查  
	    d = enchant.Dict("en_US")  
	    checkedWords=()  
	    for word in words:  
	        if not d.check(word):  
	            d.suggest(word)  
	            word=raw_input()  
	        checkedWords = (checkedWords,'05')  
	   return checkedWords  

## 六、NLTK结合Stanford NLP工具包进行文本处理
待完成