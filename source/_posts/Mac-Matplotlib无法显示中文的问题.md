---
title: 'Mac:matplotlib无法显示中文的问题'
date: 2018-09-02 13:29:05
tags: Matplotlib
category:
		- 数据分析
		- Matplotlib
---
## 前言
	网上找了好多种方法，显示中文的时候还是小框框，在我的Mac上就是不行。这是在我的Mac上可行的办法，希望对你也有用。
## 下载SimHei.ttf字体
	http://www.fontpalace.com/font-details/SimHei/
## 解决办法
	1.找到Matplotlib路径
	$ python
	> import matplotlib
	> print(matplotlib.matplotlib_fname()) # 获得matplotlib包所在文件夹路径
	/anaconda3/envs/tensorflow/lib/python3.6/site-packages/matplotlib/mpl-data/matplotlibrc
	
	2.将下载的ttf文件拷贝到此目录下
	$ cd /anaconda3/envs/tensorflow/lib/python3.6/site-packages/matplotlib/mpl-data/
	$ cd fonts/ttf
	$ cp ttf文件 . # 拷贝下载的ttf文件到此目录
	
	3. 删除字体缓存文件
	$ rm -rf ~/.matplotlib/*.cache
	
	4. 字体参数设置
	$ cd /anaconda3/envs/tensorflow/lib/python3.6/site-packages/matplotlib/mpl-data
	$ vi matplotlibrc
	font.family         : sans-serif 
	font.sans-serif     : SimHei
	axes.unicode_minus  : False # use unicode for the minus symbol
	# 意思是知道字库族为 sans-serif，同时添加“SimHei”即宋体到字库族列表中，同时将找到axes.unicode_minus，将True改为False，作用就是解决负号’-‘显示为方块的问题
	
	5. 字体显示
	有中文出现的情况，需要 u'内容'，比如 plt.xlabel(u'横坐标')
	同时还需在开头添加代码行：#coding:utf-8
	
	注意：
	但是我走到这一步后运行，字体显示仍未小框框。提示：
	【UserWarning: findfont: Font family [‘sans-serifs’] not found. Falling back to Bitstream Vera Sans】
	说明还是没有找到字体，所以我进一步进行配置。

	6.配置fontList.json
	# 在ttflist文件添加"SimHei"字体集
	
	$ cd /Users/apple/.matplotlib
	$ vi fontList.json
![](http://p2lakvkq0.bkt.clouddn.com/matplotlib_fonts.png)
	图片红框中的ttf文件是我后加的，说明之前这个ttf文件不在其中，所以自然找不到这个字体。
	根据自己的位置在文件中添加自己的ttf文件
	添加如下配置（参考文件中的配置）：
	
	{
      "fname": "/anaconda3/envs/tensorflow/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf/SimHei.ttf",
      "name": "SimHei",
      "style": "normal",
      "variant": "normal",
      "weight": 400,
      "stretch": "normal",
      "size": "scalable",
      "_class": "FontEntry"
    },
	
	之后重启项目，matplotlib即可显示中文字体了。
## 查看matplotlib支持的字体集
	$ python 
	> import matplotlib
	> xxx=sorted(f.name for f in matplotlib.font_manager.fontManager.ttflist)
	> for i in xxx:
		  	print(i)
		  	
