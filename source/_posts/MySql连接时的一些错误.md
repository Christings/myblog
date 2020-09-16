---
title: MySql连接时的一些错误
date: 2018-08-16 11:29:03
tags: [数据库]
categories:
		- 数据库
		- MySql
---
由于我的电脑时Mac的，所以只介绍Mac上的解决办法了。
### 1.ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
	解决办法：
	$ sudo cd /usr/local/mysql/data
	$ rm -rf ib_logfile0
	$ rm -rf ib_logfile1
	停止mysql服务；
	$ cd /usr/local/mysql/bin/
	$ sudo su
	$ ./mysqld_safe --skip-grant-tables & # 禁止mysql验证功能
	# 回车后mysql会自动重启
	$ ./mysql
	> FLUSH PRIVILEGES; 
	> ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码'; 
### 2.2059 - Authentication plugin 'caching_sha2_password' cannot be loaded: dlopen(../Frameworks/caching_sha2_password.so, 2): image not found
	
	解决办法：
	$ mysql -u root -p
	> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
	

