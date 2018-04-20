---
title: Python--利用wordloud制作词云
date: 2018-01-26 17:46:54
tags: [wordcloud]
categories:
		- 数据分析
		- wordcloud
---
> 本文首发于我的博客: [gongyanli.com](http://gongyanli.com/Python-%E5%88%A9%E7%94%A8wordloud%E5%88%B6%E4%BD%9C%E8%AF%8D%E4%BA%91/)  
> 词云github: [https://github.com/amueller/word_cloud](https://github.com/amueller/word_cloud)  
>词云官方地址: [https://amueller.github.io/word_cloud/](https://amueller.github.io/word_cloud/)


前言：第一次使用wordcloud，然后记录了自己的点滴，使用其实很简单。我输出的图形看着不太美观，后续可以深入研究，输出自己想要的形状。

## 一、安装

	pip install wordcloud
## 二、准备数据

我在维基百科找了一篇文章复制到txt中，文章地址[https://en.wikipedia.org/wiki/Australian_Defence_Force](https://en.wikipedia.org/wiki/Australian_Defence_Force)
## 三、制作词云
	
	`from wordcloud import WordCloud  # 导入wordcloud
	import matplotlib.pyplot as plt

	filename = "australian-defence-force.txt" # 打开txt文件
	with open(filename) as f:
    	mytext = f.read()  # 读取文件
    	wordcloud = WordCloud().generate(mytext)  # 生成词云
    	plt.imshow(wordcloud, interpolation='bilinear')  # 利用plt把词云可视化显示
    	plt.axis('off')
    	plt.show()
		# print(mytext)
`
## 四、出图
![](http://p2lakvkq0.bkt.clouddn.com/wordcloud.jpg)
## 五、注意事项
报错 ImportError: cannot import name WordCloud
创建python文件的时候我把文件取名为wordcloud.py，之后导入wordcloud时一直出错，重新取一个名字就ok了。
## 六、进阶
如果想要进阶，可参考此博客[Python词云 wordcloud 十五分钟入门与进阶](http://blog.csdn.net/fontthrone/article/details/72775865)，哈哈，我是还没有时间细看这篇博客了，大致瞟了一眼，觉得不错，空下来细看。
