---
title: Django中关于distinct的使用方法
date: 2018-08-31 11:50:26
tags: Django
category:
		- Python3
		- Django
---
## 一.问题
	我用distinct的时候出现了以下问题，DISTINCT ON fields is not supported by this database backend
## 二.解决
	文档中的用法：
	Author.objects.distinct()
	Entry.objects.order_by('pub_date').distinct('pub_date')
	Entry.objects.order_by('blog').distinct('blog')
	Entry.objects.order_by('author', 'pub_date').distinct('author', 'pub_date')
	Entry.objects.order_by('blog__name', 'mod_date').distinct('blog__name', 'mod_date')
	Entry.objects.order_by('author', 'pub_date').distinct('author')
	
	但是根据文档中的用法却出了上述问题
	
	后来找到原因，如果是使用mysql的话，distinct()中不能使用任何参数，参数应该在value()中使用
	正确使用方法：
	obj= Category.objects.values('parentcode','email').distinct()
	obj= Category.objects.values('parentcode').distinct()
	
	