---
title: hexo--themes主题无法进行备份
date: 2018-05-29 15:00:36
tags: Hexo
categories:
		- 博客
		- Hexo
---
> 参考博客：[https://hoxis.coding.me/hexo-backup-theme-dir.html]()

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