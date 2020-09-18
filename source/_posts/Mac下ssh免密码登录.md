---
title: Mac下ssh免密码登录
date: 2018-06-11 18:16:42
tags: ssh
categories:
        - 工具篇
        - ssh
---
## 本地生成公钥和私钥（客户端Mac）
	
	$ ssh-keygen -t rsa
	$ cd ~/.ssh
	$ vi id_rsa.pub  # 复制公钥
## 远程主机上添加信任的公钥（远程主机Mac或Linux）
	$ cd ~/.ssh
	$ vi authorized_keys # 将刚才复制的公钥拷贝进去

此时，再登录就不需要密码了。

