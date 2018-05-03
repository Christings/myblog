---
title: Linux操作总结
date: 2018-03-13 12:21:25
tags: [Linux]
categories:
		- Linux
		- 基本命令
---
总结一下自己工作中常用到的一些Linux命令，首先对自己的知识进行巩固，如果对大家还有一些用处的话，那我将感到不胜荣幸。文章没有系统性的讲解Linux命令，只是我自己常用到的，如果以后有时间的话，可以整理一个系统性的总结。
我的Linux系统是centos7，用户有root和lilly，如果后面看到有lilly，这就是我直接代替用户名的地方了，不再一一强调了哈。
### 1.切换用户
	
	su -- 切换到root用户
	su lilly -- 切换到lilly用户
### 2.创建用户及密码
	
	useradd lilly -- 如果只单纯创建用户而没有创建密码，将无法进行登录
	passwd lilly
### 3.删除用户
	
	userdel lilly -- 删除lilly用户
	userdel -r lilly -- 删除lilly用户及home下对应的文件夹
### 4.删除文件/文件夹
	
	rm -r   xxx
	rm -rf  xxx -- 强制删除
### 5.解压文件
	
	tar -xzvf xxx.xxx.tgz
### 6.移动文件

	mv xxx /usr/lib -- mv 源文件 目标文件夹
### 7.复制文件
	
	cp xxx.csv /usr/lib -- cp 源文件 目标文件夹
### 8.查看IP地址等信息
	
	ifconfig
### 9.查找文件
	find . -name "xxx" -- 当前目录下查找
	find / -name "xxx" -- 全局查找

### 10.查看某一文件如python所在目录

	which python
### 11.查看内存
	
	free -m -- 个人觉得这个命令更清晰 
	top
### 12.查看磁盘资源

	df -h
### 13.杀死进程
	
	kill -l  --  列出所有信号名称
	kill -s 9 进程号
	kill -9 进程号  --  只有-9才可以无条件终止进程，其他信号都有权利忽略
### 14.更改文件属主
	
	chown lilly test.txt -- 把txt文件的属主更改为lilly用户
### 15.修改文件用户组

	chgrp lilly test.txt -- 把txt文件的用户组改为lilly用户
### 16.当安装文件提示权限不够时，给文件添加执行权限

	chmod a+x 文件名
	chmod -R 777 12.txt -- 给txt文件赋予权限
	Linux下的文件夹和文件权限建议：
	文件夹        755
	文件          644
### 17.解决lilly is not in the sudoers files问题，可以给lilly用户添加sudo权限

	$ su - (使用这个命令，而不是单纯的su，不仅切换到root用户，还可以将环境
			变量一起带走，像root登录一样)；

	$ visudo (无空格)；

	在root    ALL=(ALL)  ALL下添加：
	  lilly   ALL=(ALL)  ALL

	保存退出
### 18.查看文件或目录的属主和组
	
	ls -al
	ls -l

----------
	总用量 20
	-rwxr-xr-x. 1 lilly lilly   73 4月  10 11:52 12.txt
	drwxrwxr-x. 8 lilly lilly  127 4月  10 10:45 app
	-rw-rw-r--. 1 lilly lilly  511 4月  10 09:35 config.ini
	-rw-rw-r--. 1 lilly lilly  652 4月   8 18:27 config.py
	drwxr-xr-x. 2 root  root    34 4月  10 10:21 logs
	-rw-rw-r--. 1 lilly lilly 1120 4月   9 13:59 manage.py
	drwxrwxr-x. 2 lilly lilly   93 4月  10 10:28 __pycache__
	-rw-rw-r--. 1 lilly lilly  741 4月   8 18:27 requirements.txt
	-rw-rw-r--. 1 lilly lilly    0 4月  10 10:18 uwsgi_supervisor.log
	drwxrwxr-x. 5 lilly lilly   69 4月   9 08:42 venv

----------
	横线代表空许可。r代表只读，w代表写，x代表可执行。注意这里共有10个位置。第一个字符指定了文件类型。在通常意义上，
	一个目录也是一个文件。如果第一个字符是横线，表示是一个非目录的文件。如果是d，表示是一个目录。例如：
	– rw- r– r–
	普通文件 文件主 组用户 其他用户

	其中每一位的权限用数字来表示。具体有这些权限：
	r(Read，读取，权限值为4)：对文件而言，具有读取文件内容的权限；对目录来说，具有浏览目 录的权限。
	w(Write,写入，权限值为2)：对文件而言，具有新增、修改文件内容的权限；对目录来说，具有删除、移动目录内文件的权限。
	x(eXecute，执行，权限值为1)：对文件而言，具有执行文件的权限；对目录了来说该用户具有进入目录的权限。

### 19.返回网页的内容
	curl i 127.0.0.1:5000
### 20.创建软连接
	ln -s 源文件 目标文件
### 21.查看网络监听情况
	两者具体区别参考:https://blog.csdn.net/jerrycqu/article/details/50187935
	netstat -apn
	ps -aux
### 22.在vi打开文件中查找内容
	/xxx   -- 正向查找
	？xxx  -- 反向查找
### 23.在vi打开文件中进行替换
	:s/sky/blue/ --  替换当前行第一个sky为blue
	:s/sky/blue/g --  替换当前行所有sky为blue
	但是我用上面的命令报错：E486: Pattern not found: x
	解决方法：
	:%s/sky/blue/ --  替换当前行第一个sky为blue
	:%s/sky/blue/ge --  替换当前行所有sky为blue
### 24.awk--处理文本文件的语言，是一个强大的文本分析工具
	通常，awk是以文件的一行为处理单位的。awk每接收文件的一行，然后执行相应的命令，来处理文本。
	awk '{[pattern] action}' {filenames}   # 行匹配语句 awk '' 只能用单引号
	awk -F  #-F相当于内置变量FS, 指定分割字符

	[lilly@localhost flask_aiot]$ awk '{print $0}' 12.txt 
	//awafnoooooooooooooooooo
	in
	goog
	ligt
	lily
	lily
	skyi
	lily
	skyi
	sky
	sky

----------
	
	[lilly@localhost flask_aiot]$ awk '{print "hello"}' 12.txt 
	hello
	hello
	hello
	hello
	hello
	hello
	hello
	hello
	hello
	hello
	hello
	hello

----------
	[lilly@localhost flask_aiot]$ awk '{if(NR>2 && NR<5) print $1}' 12.txt 
	goog
	ligt

----------
 	
	awk -F '=' '{print "svn" $2 "\t" $1 "\t" $3 }' test  -- 以等号为分隔符，然后过滤出第二部分、第一部分和第三部分
### 24.grep--文件内容过滤命令
	grep [-acinv] [--color=auto] '搜寻字符串' filename
	选项与参数：
	-a ：将 binary 文件以 text 文件的方式搜寻数据
	-c ：计算找到 '搜寻字符串' 的次数
	-i ：忽略大小写的不同，所以大小写视为相同
	-n ：顺便输出行号
	-v ：反向选择，亦即显示出没有 '搜寻字符串' 内容的那一行！
	-o ：仅显示匹配到的字符串
	--color=auto ：可以将找到的关键词部分加上颜色的显示喔！
	grep http test.txt  --  过滤出文件中含http的行
	grep ^http test.txt  --  过滤出文件中以http字符开头的行
	grep http$ test.txt  --  过滤出文件中以http字符结尾的行
	grep 't[ae]st' test.txt  --  过滤出test,tast,taste
	grep '[^e]st' test.txt  --  过滤出tast,taste,不能过滤出test
	grep -n '[^a-z]oo' test.txt  --  过滤出Foot，而不能过滤出foot 
### 25.管道符号--|
	grep sky 12.txt | grep "i"  --  过滤出sky字符串中含有i的内容
	cat 12.txt | grep -n 'sky'	--  -n表示找到后输出行号
	grep sky 12.txt  | wc -l	--	统计sky在文件12.txt中出现的次数
### 26.按照端口号进行查询，再kill
	
	lsof -i :8002
	sudo kill -9 2208 2209
### 27.文件比较
	
	diff test test.bak
	vimdiff test test.bak
### 28.查看Linux中的命令及帮助文档？待确认用法
	compgen -c
	连按两次tab

