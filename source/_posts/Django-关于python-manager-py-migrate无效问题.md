---
title: 'Django:关于python manager.py migrate无效问题'
date: 2018-08-24 12:01:15
tags: Django
category:
		- Python3
		- Django
---
## 一.问题：
	已有model，修改后重新建模，于是将migrations文件夹中除__init__.py之外其他文件都删掉，再次执行以下步骤python manage.py makemigrations确认成功，执行python manage.py migrate，提示No migrations to apply. 
	再次修改，指定表名，再次尝试，问题依旧。
## 二.排查
	python manage.py dbshell 进到数据库里面，查看是否表已存在 
	结果：表不存在
	检查migrations文件 
	结果：文件没问题
## 三.解决方案
	$ python manage.py dbshell # 进入数据库
	$ delete from django_migrations where app='your_appname';
	$ python manage.py makemigrations(若migrations文件未删除，可不执行这一步)
	$ python manage.py migrate # 成功
## 四.原因分析
	1.查看django_migrations表结构 
	建表语句： 
	CREATE TABLE "django_migrations" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "app" varchar(255) NOT NULL, "name" varchar(255) NOT NULL, "applied" datetime NOT NULL); 
	
	2.原因 
	造成多次应用migrations失败的原因是，当前model是修改过的，原来的migrations已经被我删除，但是，重新生成的migrations使用递增整数记名，所以，在django_migrations表中0001，0002等前面几个数字的文件都已被记录，在Django看来，被记录了就相当于已应用，所以，会出现刚开始的No migrations to apply.
## 五.避免方案
	删除migrations文件，请同时到数据库中删除相应记录
	可以继续生成新的migrations，旧的就不必理会了
## 六.其他

	执行python manage.py migrate之后，可以使用python manage.py sqlmigrate appname migrations_num(例如python manage.py sqlmigrate user 0002)查看当前migrations文件对应的sql语句。 
	另外，在使用上述命令查看0002文件的sql语句时发现，django会新建一个表user_new，然后插入user表中的数据，再把user表删掉，再把user_new重命名为user。所以，修改model的时候，不必担心原有数据会丢失。