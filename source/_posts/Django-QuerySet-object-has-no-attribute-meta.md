---
title: 'Django:QuerySet object has no attribute _meta'
date: 2018-08-27 16:27:32
tags: Django
category:
		- Python3
		- Django
---
## 一.问题
	QuerySet object has no attribute _meta
## 二.原因
	filter() returns a QuerySet also if only one object if found. If you want to return just a model instance, use get()
	
	edit_content = models.Wikistore.objects.filter(id=edit_id)
	form = EditorTestForm(instance=edit_content)
	
	# 将filter改为get即可。
  
  