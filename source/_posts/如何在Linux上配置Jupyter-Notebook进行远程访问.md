---
title: 如何在Linux上配置Jupyter Notebook进行远程访问
date: 2018-03-13 16:14:44
tags: [Jupyter]
categories:
		- Python3
		- Jupyter
---
本机的内存开始不够用了，所以就开始把东西都折腾到服务器了，如何在服务器上安装
Anaconda以及创建虚拟环境，我还会再写一篇来进行说明。这篇文章就先说说，安装好
虚拟环境后如何进行远程访问。我的虚拟环境用的是Anaconda，所以一般我安装用的conda。

### 1、激活虚拟环境

	$ source activate py2
### 2、安装ipython、jupyter

	$ pip install ipython
	$ pip install jupyter
	$ conda install ipython
	$ conda install jupyter
### 3、生成配置文件
	
	$ jupyter notebook --generate-config
	Writing default config to: root/.jupyterjupyter_notebook_config.py
### 4、生成密码
	
	$ ipython

----------
	   
	In [1]: from notebook.auth import passwd    
    
	In [2]: passwd()    
	Enter password:     
	Verify password:     
	Out[2]: 'sha1:43b95b731276:5d330ee6f6054613b3ab4cc59c5048ff7c70f549' 

### 4、修改默认配置文件

	$ vi /root/.jupyter/jupyter_notebook_config.py   

----------

	`c.NotebookApp.ip='*' #设置访问notebook的ip，*表示所有IP，这里设置ip为都可访问  
	c.NotebookApp.password = u'sha1:5df252f58b7f:bf65d53125bb36c085162b3780377f66d73972d1' #填写刚刚生成的密文  
	c.NotebookApp.open_browser = False # 禁止notebook启动时自动打开浏览器(在linux服务器一般都是ssh命令行访问，没有图形界面的。所以，启动也没啥用)  
	c.NotebookApp.port =8889 #指定访问的端口，默认是8888。 `
### 5、启动Jupyter Notebook

	$ jupyter notebook --allow-root
	生成ip:8889
### 6、浏览器打开ip:8889，出现jupyter的界面

	搞定！如何操作不再细说。
	
	 
	 
