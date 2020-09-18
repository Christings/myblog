---
title: 'iTerm2+sshpass:Mac下使用iTerm2免密连接远程主机'
date: 2018-08-31 11:37:49
tags: [ssh]
category:
		- 工具篇
		- ssh
---
## 一.安装iTerm2
	不再介绍
## 二.安装sshpass
	使用brew进行安装
	$ brew install https://raw.githubusercontent.com/kadwanev/bigboybrew/master/Library/Formula/sshpass.rb
	$ sshpass -h # 验证是否安装成功
## 三.设置免密
	关键是Command模式格式：
	/usr/local/bin/sshpass -p (密码) ssh -p (端口) (登录)@(服务器IP或者域名)
	
