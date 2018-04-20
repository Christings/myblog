---
title: Python3爬虫--PhantomJS安装
date: 2018-03-29 15:40:06
tags:
---
## 一、下载
	http://phantomjs.org/download.html 
## 二、安装
	
	解压文件，examlpe中有很多小例子可以试试
	把解压后的bin路径配置到path变量中
	打开cmd，输入phantomjs验证
## 三、验证

	`from selenium import webdriver
	browser = webdriver.PhantomJS(executable_path=r'E:\Program Files\phantomjs-2.1.1-windows\bin\phantomjs.exe')
	browser.get('https://www.baidu.com')
	print(browser.current_url)`

----------
	输出：https:www.baidu.com
	
	成功！