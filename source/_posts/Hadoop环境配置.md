---
title: Hadoop环境配置
date: 2018-05-02 15:59:08
tags: Hadoop
categories:
		- 大数据
		- Hadoop
---
## 一、使用ifconfig查看当前机器的ip
	我的三台机器ip如下：
	192.168.211.131
	192.168.211.132
	192.168.211.133
## 二、修改机器的hostname
	$ vim /etc/hosts
	192.168.211.131		hserver1
	192.168.211.132		hserver2
	192.168.211.133		hserver3
	
	配置完成保存后利用如下命令查看机器之间是否互相ping通
	$ ping -c 3 hserver3
## 三、给3台机器生成密钥
	$ ssh-keygen -t rsa -P ''
	在3台机器侠存入相同的文件authorized_keys
	$ touch /root/.ssh/authorized_keys
	将3台机器的公钥均合并到autorized_keys
	最后利用ssh hserver2进行测试
## 四、jdk
## 五、Hadoop
	1. 传送文件
	$ scp /home/data root@192.168.1.33:/home/new-data
	$ scp -r /home/data root@192.168.1.33:/home/new-data

	2. 新建目录
	$ mkdir /root/hadoop
	$ mkdir /root/hadoop/tmp
	$ mkdir /root/hadoop/var
	$ mkdir /root/hadoop/dfs
	$ mkdir /root/hadoop/dfs/name
	$ mkdir /root/hadoop/dfs/data

待续