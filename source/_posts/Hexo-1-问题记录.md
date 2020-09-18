---
title: Hexo-1-问题记录
date: 2018-04-13 14:13:58
tags: Hexo
categories:
		- 工具篇
		- Hexo
---
## 一、分类标签中关于大小写的bug
### 1.描述
	当我的分类标签写的是Scrapy时，打开我的博客找到Scrapy标签，点击Scrapy却出现404页面。
	当我把标签改为scrapy小写，再发布到网上，点击scrapy就不会出现404问题。
	后来发现原来是git标签生成时忽略了大写，生成的实际标签为scrapy。
	于是我来到我的Github中，找到Gladysgong.github.io/categories/爬虫/这个目录，发现实际生成的也是scrapy，所以
	原因就在这里了。
![](http://p2lakvkq0.bkt.clouddn.com/hexo_bug.jpg)
### 2.解决
	修改文件：
		cd blog/.deploy_git
		vi .git/config
		将ignorecase=true改为ignorecase=false
	删除Gladysgong.github.io中的文件并提交：
		git rm -rm *
		git commit -m "clean all files"
		git push
	Hexo再次生成及部署：
		cd ..
		hexo clean
		hexo g
		hexo d

## 二、分类和标签全部404

	npm install hexo-generator-category --save
	npm install --save hexo-generator-tag --save

## 三、themes主题无法备份

参考博客：[https://hoxis.coding.me/hexo-backup-theme-dir.html]()

在写Hexo博客时，因为遇到电脑迁移问题，所以对Hexo博客做了备份，但是发现themes主题没有进行备份，如图：
![](http://p2lakvkq0.bkt.clouddn.com/hexo_themes.jpg)

解决：

	$ git rm --cached themes/yilia
	rm 'themes/yilia'
	
	$ git status
原因：

	这是因为用到了 git 的子模块（git submodule）功能（你在你的 git 项目里 clone 的别人的项目）。
	
	在你的主项目的 git 库里，子模块只是一个 HEAD 指针，指向子模块的 commit。
	
	这个功能的意义：
	
	在这里，如果你需要修改 next 主题（可能需要很多文件），又想保证能够随时更新最新版本，其实用子模块功能是很方便的。
	
	只需要 clone 下来新建一个 branch，用来自己用，每次官方更新 pull 到另一个分支，merge 一下就行。
	
	相当于把一个大项目分成多个小项目，尽可能减少项目之间的关联，方便调试和修改。
	
	这里我偷懒直接将子模块删除，将整个仓库进行备份了。
	
	比较优雅的做法可以参考：https://github.com/iissnan/hexo-theme-next/issues/328