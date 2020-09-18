---
title: 如何更新GitHub上自己Fork的项目
date: 2018-05-04 09:34:12
tags: git
categories:
		- 工具篇
		- git
---
当我们在GitHub上fork别人的项目后，可以方便我们学习使用，但是当别人更新了源码后，而我们fork的分支却不会一起更新，需要手动更新。
以我的账号-Gladysgong为例，fork的项目是https://github.com/qq547276542/Agriculture_KnowledgeGraph.git
## 一、fork项目后clone到本地
	$ git clone https://github.com/qq547276542/Agriculture_KnowledgeGraph.git
	$ cd Agriculture_KnowledgeGraph
## 二、增加远程原始的分支到本地
	$ git remote -v

	origin  https://github.com/Gladysgong/Agriculture_KnowledgeGraph.git (fetch)
	origin  https://github.com/Gladysgong/Agriculture_KnowledgeGraph.git (push)

	$ git remote add upstream https://github.com/qq547276542/Agriculture_KnowledgeGraph.git
	$ git remote -v

	origin  https://github.com/Gladysgong/Agriculture_KnowledgeGraph.git (fetch)
	origin  https://github.com/Gladysgong/Agriculture_KnowledgeGraph.git (push)
	upstream        git@github.com:qq547276542/Agriculture_KnowledgeGraph.git (fetch
	upstream        git@github.com:qq547276542/Agriculture_KnowledgeGraph.git (push)
## 三、fetch原始分支的新版本到本地
	$ git fetch upstream 
## 四、合并两个版本的代码
	$ git merge upstream/master
## 五、把最新代码提交到GitHub我们自己的账号（Gladysgong）上
	$ git push origin master