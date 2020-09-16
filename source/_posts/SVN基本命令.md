---
title: SVN基本命令
date: 2018-06-11 10:42:30
tags: SVN
categories:
		- 版本管理
		- SVN
---
1.帮助文档

	$ svn --help
	$ svn help
	$ svn help ci
2.将文件checkout到本地

	$ svn checkout PATH
	$ svn checkout http://svn.sogou-inc.com/svn/operation/op-tools/build build
	简写：svn co
3.往版本库中添加新的文件

	$ svn add file
	$ svn add test.txt
	$ svn add *.txt
4.将修改文件提交到版本库

	$ svn commit -m "LogMessage" [-N] [--no-unlock] PATH(如果选择了保持锁，就使用–no-unlock开关)
	$ svn commit -m "add test" test.txt
	简写：svn ci
5.加锁/解锁

	$ svn lock -m "LockMessage" [--force] PATH
	$ svn lock -m "lock test" test.php
	$ svn unlock PATH
6.更新版本
	
	$ svn update -r m PATH
	$ svn update  如果后面没有目录，默认将当前目录以及子目录下的所有文件都更新到最新版本。
	$svn update -r 200 test.php (将版本库中的文件test.php还原到版本200)
	$ svn update test.php  (更新，于版本库同步。如果在提交的时候提示过期的话，是因为冲突，需要先update，修改文件，然后清除svn resolved，最后再提交commit)
	简写：svn up
7.查看文件或者目录状态

	$ svn status path（目录下的文件和子目录的状态，正常状态不显示）
	# 不在svn的控制中；M：内容被修改；C：发生冲突；A：预定加入到版本库；K：被锁定
	$ svn status -v path(显示文件和子目录状态)
	# 第一列保持相同，第二列显示工作版本号，第三和第四列显示最后一次修改的版本号和修改人。
	# svn status、svn diff和 svn revert这三条命令在没有网络的情况下也可以执行的，原因是svn在本地的.svn中保留了本地版本的原始拷贝。
	简写：svn st
8.删除文件

	$ svn delete path -m “delete test“
	$ svn delete svn://192.168.1.1/pro/domain/test.php -m “delete test file”
	
	$ svn delete test.php 
	$ svn ci -m ‘delete test‘
	简写：svn (del, remove, rm)
9.查看日志

	$ svn log PATH
	$ svn log test.txt # 显示这个文件的所有修改记录，及其版本号的变化
10.查看文件详细信息
	
	$ svn info PATH
	$ svn info test.txt
11.比较差异

	$ svn diff PATH  # 将修改的文件与基础版本比较
 	$ svn diff test.txt
	$ svn diff -r m:n PATH # 对版本m和版本n比较差异
	$ svn diff -r 200:201 test.txt
	简写：svn di
12.将两个版本之间的差异合并到当前文件
	
	$ svn merge -r m:n PATH
	$ svn merge -r 200:201 test.txt 将版本200与201之间的差异合并到当前文件，一般需要处理一下
13.版本库下的文件和目录列表

	$ svn list path # 显示path目录下的所有属于版本库的文件和目录
	简写：svn ls
14.代码库url变更
	
	$ svn switch/sw
15.恢复本地修改
	
	$ svn revert
16.解决冲突

	$ svn resolved