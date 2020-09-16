---
title: 'Django:关于django.db.utils.InternalError:Table wiki already exists'
date: 2018-08-31 14:14:32
tags: Django
category:
		- Python3
		- Django
---
## 一.错误代码
	django.db.utils.InternalError: (1050, "Table 'wiki' already exists")
## 二.解决
	$ python manage.py migrate wiki --fake 
	# wiki是自己的应用名
	$ python manage.py migrate
