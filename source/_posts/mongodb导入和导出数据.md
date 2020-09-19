---
title: MongoDB导入和导出数据
date: 2018-03-22 13:03:19
tags: [MongoDB]
categories:
		- 数据库
		- MongoDB
---
MongoDB数据库关于数据导入导出的操作是在cmd下进行的，所以我们首先要打开cmd。
## 导出数据


	mongoexport --db 数据库名 --collection 表名 -o 文件名 --type csv/json -f field
	mongoexport --db aiot --collection user   -o e:\data\user1.csv --type csv -f email,username,password
## 导出数据

	mongoexport --db 数据库名 -c 表名 -o 文件名 -f fieldname --headerline --type csv/json -f field
	mongoexport --db aiot -c user -o e:\data\user1.csv --headerline --type csv -f email,username,password
## 数据库备份
	
	mongodump -h dbhost -d dbname -o dbdirectory
## 数据库恢复

	mongostore -h dbhost -d dbname --dir dbdirectiry


