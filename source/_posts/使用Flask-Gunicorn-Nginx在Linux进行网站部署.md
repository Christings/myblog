---
title: 使用Flask+Gunicorn+Nginx在Linux进行网站部署
date: 2018-04-13 14:33:57
tags: [Python,Flask,Gunicorn,Nginx]
categories:
		- Linux
		- 网站部署
---
>本文首发于我的博客：[http://gongyanli.com/](http://gongyanli.com/%E4%BD%BF%E7%94%A8Flask-Gunicorn-Nginx%E5%9C%A8Linux%E8%BF%9B%E8%A1%8C%E7%BD%91%E7%AB%99%E9%83%A8%E7%BD%B2/)   
>代码传送门：[https://github.com/Gladysgong/flask_scrapy_aiot](https://github.com/Gladysgong/flask_scrapy_aiot)    
>简书: [https://www.jianshu.com/p/7da2e5892dd6](https://www.jianshu.com/p/7da2e5892dd6)  
>CSDN: [https://blog.csdn.net/u012052168/article/details/79985613](https://blog.csdn.net/u012052168/article/details/79985613)

## 一、前言
这个网站部署属于很心酸的过程，至于如何心酸我必须得说出来，给我自己的时间一个交代。开始我使用Flask+uWsgi+Nginx进行配置的，哪哪儿都配置好了，Nginx欢迎页也可以访问，Flask本身也没问题，但就是uWsgi访问不了。
因为哪里配置都没问题，但就是无法访问，我Google、baidu各种查了好久，都解决不了。有说权限问题的，但是我都换到root下了，有说哪里哪里加个plugins的，对于我还是不行。后来我看见有人说，他也遇到这个问题解决不了，后来他就搁置不弄了，过了一星期左右，打开
自己就连接上了。于是我想了想，对于这种我该放弃了。
网上看了看，说Gunicorn比uWsgi好用，于是我就换成了Gunicorn，然后分分钟成功，可是我却在这上面干耗了一天。
顺便提一下，代码中flask_aiot为网站文件，scrapy_aiot是我的爬虫文件，用来抓取网站所用的数据，有兴趣可以看一下，当时写的好像不是很完善，有不对的地方留言告诉就ok，我改。
## 二、环境和目录结构
	Centos7
	Python3
	Flask：是一个用Python实现的Web开发的微服务框架
	Guicorn：Web服务器，和UWsgi类似
	Nginx：反向代理

----------
	flask_aiot
		├── app
		├── config.py
		├── manage.py
		├── requirements.txt
		└── venv
		    ├── bin
		    ├── include
		    ├── lib
		    └── share

## 三、安装基础环境
我用的是Python3，而现在Centos下默认的还是Python2，所以我用Anaconda安装了一个Python3，很方便。
不同的项目可能会需要不同的依赖包，所以我用了VirtualEnv来管理依赖。

	pip install virtualenv
	创建虚拟环境并激活
	[root@localhost flask_aiot]# source venv/bin/activate

## 四、安装Flask
	直接在虚拟环境下安装requirements.txt即可，一次性把依赖都安装齐全。
	(venv) [root@localhost flask_aiot]# pip install -r requirements.txt 
## 五、项目文件
在生产环境中，我们的Flask项目是做在包内的，在包外采用Flask Script写一个manage.py文件作为启动文件，这样方便支持各种项目。manage.py文件如下：

	`
	#!/usr/bin/env python
	import os
	
	if os.path.exists('.env'):
	    print('Importing environment from .env...')
	    for line in open('.env'):
	        var = line.strip().split('=')
	        if len(var) == 2:
	            os.environ[var[0]] = var[1]
	
	# from app import create_app, db
	from app import create_app, mongo
	from app.models import User, AllProductPrice, Ny135, Chinacwa, Iot
	from flask_script import Manager, Shell
	from flask_migrate import Migrate, MigrateCommand
	
	
	app = create_app(os.getenv('FLASK_CONFIG') or 'default')
	manager = Manager(app)
	migrate = Migrate(app, mongo)
	
	
	def make_shell_context():
	    return dict(app=app, db=mongo, User=User, AllProductPrice=AllProductPrice,
	                Ny135=Ny135, Chinacwa=Chinacwa, Iot=Iot)
	
	
	manager.add_command("shell", Shell(make_context=make_shell_context))
	manager.add_command('db', MigrateCommand)
	
	
	@manager.command
	def test():
	    """Run the unit tests."""
	    import unittest
	    tests = unittest.TestLoader().discover('tests')
	    unittest.TextTestRunner(verbosity=2).run(tests)
	
	
	if __name__ == '__main__':
	    manager.run()
	`
此时我们可以直接在虚拟环境中运行Flask：

	默认地址和端口启动：
	(venv) [root@localhost flask_aiot]# python manage.py runserver  
	任意地址和8099端口启动：
	(venv) [root@localhost flask_aiot]# python manage.py runserver -h 0.0.0.0 -p 8099
	运行：
	curl i 127.0.0.1:8099  # 看到网站返回的内容

## 六、安装Gunicorn
Flask的生产环境比较成熟的有Gunicorn和uWSGI,无奈我一直配置uWSGI不成功，所以换成了Gunicorn，而且和uWSGI相比起来，Gunicorn的配置真的蛮简单的。
	
	(venv) [root@localhost flask_aiot]# pip install gunicorn
	运行：
	(venv) [root@localhost flask_aiot]# gunicorn -w 4 -b 127.0.0.1:8001 manage:app
	curl i 127.0.0.1:8001  # 看到网站返回的内容

## 七、Nginx
### 1、安装
	网上好多用yum install nginx来进行安装的，但是目前centos7上已经不支持了，所以我用安装包进行安装的。
### 2、配置文件
	vi /etc/nginx/conf.d/default.conf

	default.conf如下：
	server {
	    listen       8099;
	    server_name  10.200.116.13;
	    # server_name  111.203.20.92;

	    location / {
	      #  include uwsgi_params;
	      #  uwsgi_pass  127.0.0.1:8001; # 指向uwsgi 所应用的内部地址,所有请求将转发给uwsgi 处理
	      #  uwsgi_param UWSGI_PYHOME /home/lilly/www/flask_scrapy_aiot/flask_aiot/venv; # 指向虚拟环境目录
	      #  uwsgi_param UWSGI_CHDIR  /home/lilly/www/flask_scrapy_aiot/flask_aiot; # 指向网站根目录
	      #  uwsgi_param UWSGI_SCRIPT manage:app; # 指定启动程序
	      #  uwsgi_read_timeout 100;
	        proxy_pass http://localhost:8001;
	        proxy_redirect          off;
	        proxy_set_header        Host                    $host;
	        proxy_set_header        X-Real-IP               $remote_addr;
	        proxy_set_header        X-Forwarded-For         $proxy_add_x_forwarded_for;
	        proxy_set_header        X-Forwared-Proto        $scheme;
	      #  root   /usr/share/nginx/html;
	      #  index  index.html index.htm;
	    }
	

----------
	vi /etc/nginx/nginx.conf 

	nginx.conf如下：
	user  nginx;
	worker_processes  1;
	
	error_log  /var/log/nginx/error.log warn;
	pid        /var/run/nginx.pid;
	
	
	events {
	    worker_connections  1024;
	}
	
	
	http {
	    include       /etc/nginx/mime.types;
	    default_type  application/octet-stream;
	
	    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
	                      '$status $body_bytes_sent "$http_referer" '
	                      '"$http_user_agent" "$http_x_forwarded_for"';
	
	    access_log  /var/log/nginx/access.log  main;
	
	    sendfile        on;
	    #tcp_nopush     on;
	
	    keepalive_timeout  65;
	
	    #gzip  on;
	
	    include /etc/nginx/conf.d/*.conf;   # 这句话很重要，相当于引入了上面的default.conf配置文件
	}
### 3、重启Nginx
	sudo service nginx restart
	sudo systemctl restart  nginx.service
	我用以上方式启动的时候总是提示：Job for nginx.service failed because the control process exited with error code. 
	See "systemctl status nginx.service" and "journalctl -xe" for details.
	我查看的时候说：nginx: [emerg] open() "/var/run/nginx.pid" failed (13: Permission denied)
	但是我都是用root来启动的了，不应该存在这种问题啊。
	所以我是直接nginx这样启动的，确实nginx是起来了的。
### 4、访问网站
	
	curl i 10.200.116.13：8099  # 返回网站内容
## 八、Supervisor
如果想要当进程意外关闭后能自动重启，应该再配置一个Supervisor来实现。目前Supervisor只支持Python2，我看官方说目前Python3已经在进行中，应该很快就能支持了。我在这个部署里就没做这块了，即使在Python3下面也可以用Supervisor，因为他只是调进程而已，想做的话可以网上找找文档，不难。
## 九、后续
Nginx等一系列部署好后，我打开网站还发现了一个bug，当然这是我程序的问题。bug如下：

	当我10.200.116.13：8099时停留在首页，当我点击网站的某一按钮，跳转到另一个页面，出现404错误，而此时我看网站的url为
	10.200.116.13/show
	这也就意味着网站跳转时没把8099的端口给带上，而我手动在地址栏输入10.200.116.13：8099/show时，页面是能够正常显示的。
	说明8099的端口有些地方是静态的，没有动态更着走。把下面的语句1改为语句2就可以了。

----------

	语句1:<li><a href="/show_ny135" class="hvr-sweep-to-bottom">中国农业物联网</a></li>#}

    语句2：<li><a href="{{ url_for('main.ShowNy135View') }}" class="hvr-sweep-to-bottom">中国农业物联网</a></li>
     

