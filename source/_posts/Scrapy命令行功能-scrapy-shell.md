---
title: Scrapy命令行功能--scrapy shell
date: 2018-01-12 15:36:05
tags: [Scrapy]
categories:
		- 爬虫
		- Scrapy
---
>本文首发于我的博客：[gongyanli.com](http://gongyanli.com/Scrapy%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%8A%9F%E8%83%BD-scrapy-shell/)

前言:本文主要讲解Scrapy的命令行功能，Scrapy提供了两种类型的命令。
	
	1.全局命令
	2.项目命令，必须在Scrapy项目中运行
<!--more-->
## 全局命令
	1.startproject
	> scrapy startproject [project_name]
	> 创建名为mySpider的Scrapy项目
	$ scrapy startproject mySpider
	
	2.settings
	> scrapy settings [options]
	> 当项目运行时，输出项目的设定值，否则输出Scrapy的默认设定值
	$ scrapy settings --get BOT_NAME scrapybot
	$ scrapy settings --get DOWNLOAD_DELAY 0

	3.runspider
	> scrapy runspider <spider_file.py>
	> 未创建项目的情况下，运行一个编写好的spider模块
	$ scrapy runspider testSpider.py
	
	4.shell
	> scrapy shell [url]
	> 启动Scrapy shell，url可选
	$ scrapy shell "http://www.baidu.com"

	5.fetch
	> scrapy fetch <url>
	> 使用Scrapy下载器(downloader)下载给定的url，并将获取的内容输出
	> 命令以spider下载页面的方式获取页面，如果在项目中运行，fetch将会使用项目中spider的属性访问；如果在非项目中运行，则会使用默认Scrapy downloader的设定
	$ scrapy fetch --nolog "http://www.baidu.com"
	$ scrapy fetch --nolog --headers "http://www.baidu.com"

	6.view
	> scrapy view <url>
	> 在浏览器中打开url，并以Scrapy spider获取到的形式显示
	> 与view(response)效果一样
	$ scrapy view "http://www.baidu.com"

	7.version
	> scrapy version [-v]
	> 输出Scrapy版本
	> 配合-v运行时，同时输出Python、Twisted以及平台信息，方便bug提交
	
	8.bench
	> scrapy bench
	> 用于运行benchmark测试，测试Scrapy在硬件上的效率
## 项目命令
	1.crawl
	> scrapy crawl <spider>
	> 使用spider进行爬取
	$ scrapy crawl mySpider

	2.check
	> scrapy check [-l] <spider>
	> 运行contract检查
	$ scrapy check -l
	
	3.list
	> scrapy list
	> 列出当前项目中所有的可用的spider，每行输出一个
	
	4.edit
	> scrapy edit <spider>
	> 使用设定的编辑器编辑给定的spider，此命令仅提供一个快捷方式，可自由选择编辑器
	$ scrapy edit mySpider

	5.parse
	> scrapy parse <url> [options]
	> 获取给定的url并使用相应的spider分析处理
	> 如果提供--callback选项，则使用spider中的解析方法进行处理。
	> 支持的选项：
	>>> --spider=SPIDER:跳过自动检测spider并强制使用特定的spider
	>>> --a NAME=VALUE:设定spider参数（可能被重复）
	>>> --callback or -c:spider中用于解析response的回调函数
	>>> --pipelines:在pipelin中处理item
	>>> --rules or r:使用CrawlSpider规则来发现用于解析response的回调函数
	>>> --noitems:不显示爬取到的item
	>>> --nolinks:不显示提取到的链接
	>>> --nocolour:避免使用pygments对输出着色
	>>> --depth or -d:指定跟进链接请求的层次数（默认：1）
	>>> --verbose or -v:显示每个请求的详细信息
	$ scrapy parse "http://www.baidu.com"

	6.genspider
	> scrapy genspider [-t template] <name> <domain>
	> 在当前项目中创建spider
	> 这是创建spider的快捷方式，可使用提前定义好的模板来生成spider，也可自己创建spider的源文件
	$ scrapy genspider -l
	> 显示有哪些模板
	$ scrapy genspider -d basic
	$ scrapy genspider -t basic example example.com

	7.deploy
	> scrapy deploy [<target:project> | -l <target> | -L]
	> 部署项目到Scrapyd服务