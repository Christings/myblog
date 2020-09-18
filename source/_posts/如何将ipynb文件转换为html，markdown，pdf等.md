---
title: 如何将ipynb文件转换为html，markdown，pdf等
date: 2018-09-09 22:25:54
tags: Jupyter
category:
		- Python
		- 工具篇
		- Jupyter
---
## 前言
	本文讲将jupyter notebook中的文件（xxx.ipynb）转换成html，md，pdf等格式呢。
	如果使用下列命令有误的话，建议安装jupyter相关插件：
	$ pip install jupyter_contrib_nbextensions

## 一.ipynb转为html格式
	$ jupyter nbconvert --to html notebook.ipynb
	
	jupyter中提供了一些命令，可以对生成的html格式进行配置：
	$ jupyter nbconvert --to html --template full notebook.ipynb

	默认配置，提供完整的静态html格式，交互性更强。
	$ jupyter nbconvert --to html --template basic notebook.ipynb
	简化的html，用于嵌入网页、博客等，这不包括html标题。

## 二.ipynb转换为md格式
	$ jupyter nbconvert --to markdown notebook.ipynb
	简单的Markdown格式输出，cell单元不受影响，代码cell缩进4个空格。
## 三.ipynb转换为tex格式
	$ jupyter nbconvert --to letex notebook.ipynb
	
	Letex导出格式，生成后缀名为NOTEBOOK_NAME.tex的文件。jupyter提供的额外模板配置为：
	$ jupyter nbconvert --to letex -template article notebook.ipynb
	
	默认配置，Latex文章。
	$ jupyter nbconvert --to letex -template report notebook.ipynb

	Latex报告，提供目录和章节。
	$ jupyter nbconvert --to letex -template basic notebook.ipynb
	最基本的Latex输出，经常用来自定义配置。

## 四.jupyter转换为pdf格式
	$ jupyter nbconvert --to pdf notebook.ipynb

	转换为pdf格式分模板配置与latex配置是一样的。但是直接转换为pdf格式经常会出现xelatex错误：
	该错误提示没有安装xelatex。所以，我们需要提前安装xelatex，方法是安装texLive套装：

	$ sudo apt-get install texlive-full
	texlive-full的安装包有点大，约1G多。

## 五.简单的转换方法
	ipynb转换为html、md、pdf等格式，还有另一种更简单的方法：在jupyter notebook中，选择File->Download as，直接选择需要转换的格式就可以了。需要注意的是，转换为pdf格式之前，同样要保证已经安装了xelatex。