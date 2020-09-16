---
title: MySql中的常见错误
date: 2018-08-15 13:17:25
tags: [数据库]
categories:
		- 数据库
		- MySql
---
## 一.MySql server has gone away

### 1.原因

	Mysql server服务器连接超时，并且关闭了与客户端的连接导致的；
	默认情况下，如果在8小时没有对mysql进行查询请求的话，服务器就会自动断开连接。
	
	发送给服务器的查询是错误的或者传送的数据太大；

### 2.解决方法
	修改全局变量 wait_timeout和interactive_timeout两个变量的值
	$ mysql -u root -p
	$ mysql>show variables like '%timeout';
	$ mysql>set global wait_timeout = 2880000;
	$ mysql>set global interactive_timeout = 2880000;
	
	interactive_timeout：交互式客户端连接的超时时间。
	wait_timeout：是非交互式的超时时间。
	client_interactive：运行mysql server 在交互式超时时间interactive_timeout（而不是wait_timeout）时间之后断开连接。
	
	修改变量max_allowed_packet的大小
	$ mysql> set global max_allowed_packet = 64M;
## 二.'latin-1' codec can't encode character
### 1.原因
	pymysql 正常情况下会尝试将所有的内容转为latin1字符集处理
### 2.解决方法
	设置连接和游标的charset为你所希望的编码，如utf8；
	db = pymysql.connect("localhost","root","00000000","TESTDB" ,use_unicode=True, charset="utf8")
	注意：如果charset="utf-8"也会报错，错误信息为【AttributeError: 'NoneType' object has no attribute 'encoding'】
	可以看出是编码格式的问题，原来connect中的charset是"utf8"，而不是"utf-8"。同时port的需要number而不是string。
