---
title: Hexo+Github Pages+yilia搭建博客
date: 2018-01-05 18:06:40
toc: true
tags:
        - Hexo
        - Github
categories:
		- 博客
		- Hexo
---
>本文首发于我的博客：[gongyanli.com](http://gongyanli.com/Hexo-Github-Pages%E6%90%AD%E5%BB%BA%E5%8D%9A%E5%AE%A2/)  
>github: [https://github.com/Gladysgong/Gladysgong.github.io](https://github.com/Gladysgong/Gladysgong.github.io)
 
### 一、快速创建个人Github Pages
#### 1.什么是Github Pages

	GitHub Pages 本用于介绍托管在GitHub的项目，不过由于他的空间免费稳定，很适合用来搭建一个博客。

	每个帐号只能有一个仓库来存放个人主页，而且仓库的名字必须是username/username.github.io，这是特殊的命名约定。你可以通过http://username.github.io 来访问你的个人主页，比如我的就是gladysgong.github.io。

	这里特别提醒一下，需要注意的个人主页的网站内容是在master分支下的。

#### 2.创建自己的Github Pages
	新建代码仓库(repository),此处命名格式有限制的，形如username.github.io
![这里写图片描述](http://img.blog.csdn.net/20180105161022371?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjA1MjE2OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](http://img.blog.csdn.net/20180105161115747?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjA1MjE2OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
		 
	github page site已经建好，浏览器输入例如http://username.github.io,(该示例界面为空)，可以自己push一个index页面来显示内容，开启你的blog把！
我的blog是[gladysgong.github.io](gladysgong.github.io),可以打开看一下哟！如果文章对您有帮助，右侧fork进入star我一下，我会美美哒一天。
	
#### 3.如何搭建一个独立博客——简明Github Pages与Hexo教程



### 二、搭建环境
1.安装[node.js](https://nodejs.org/en/)
2.安装npm
3.安装git，如果前面要commit、push，那之前就应该安装
4.github账户配置
### 三、Hexo安装
	$ cd d:/hexo
	$ npm install hexo-cli -g
	$ hexo init blog
	$ cd blog
	$ npm install
	$ hexo g # 或者hexo generate
	$ hexo s # 或者hexo server，可以在http://localhost:4000/ 查看（hexo s -p 8023端口被占用时）
![这里写图片描述](http://img.blog.csdn.net/20180105162744799?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjA1MjE2OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
### 四、Hexo主题切换
	Hexo默认主题是landscape，大家可以切换成为next或者yilia的风格
	1.安装主题
	
	$ hexo clean
	$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia

2.启用主题

	修改Hexo目录下的_config.yml配置文件中的theme属性，将其设置为yilia。
3.更新主题

	$ cd themes/yilia
	$ git pull
	$ hexo g # 生成
	$ hexo s # 启动本地web服务器
4.打开http://localhost:8023/ ，我们就会看到一个全新的界面。
### 五、部署Hexo到Github Pages
1.部署Hexo到Github Pages上的原理

    a.第一步中我们在Github上创建了一个特殊的repo（gladysgong.github.io）一个最大的特点就是master中的html静态文件，可以通过链接http://gladysgong.github.io来直接访问。
    b.Hexo -g 会生成一个静态网站（第一次会生成一个public目录），这个静态文件可以直接访问。
    c.将hexo生成的静态网站，提交(git commit)到github上。
2.使用hexo deploy部署到github

	在配置文件_config.xml中以下改动：
	deploy:
	  type: git
	  repo: git@github.com:gladysgong/gladysgong.github.io.git
	  branch: master
3.安装扩展（不安装会出错）
	
	$ npm install hexo-deployer-git --save
4.在命令行中执行
	
	hexo d
	
### 六、申请域名https://sg.godaddy.com/zh/ 
可以申请的地方很多，我选择的是[goDaddy](https://sg.godaddy.com/zh/)，注册登录选择域名，支付宝付款，ok。
### 七、DNS解析
1.注册[DNSpod](https://www.dnspod.cn/)
2.进入域名解析，输入你的域名，添加记录进行配置
![这里写图片描述](http://img.blog.csdn.net/20180105165317195?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjA1MjE2OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
3.记录类型
		
	记录类型A代表指向github pages所提供的ip
	记录类型CNAME代表指向username.github.io
	用A类型的话，当github pages所提供的ip改变的话，你就需要来改变此处的ip，用CNAME则不需要。
4.创建CNAME
		
	$ cd source
	$ 创建CNAME
	$ 内容为你的域名（比如我的：gongyanli.com）
	$ commit & push
### 八、hexo部署后把原来的仓库覆盖
1.使用Hexo搭建博客需要区分【博客源代码】和【博客生成代码】
	

    『博客源代码』：Hexo的源码，包括themes目录（博客模板），source目录(使用MarkDown写的博客)等

    『博客生成代码』：执行hexo generate或者hexo server命令生成的代码，是Hexo自动生成的，在public目录里面。
	
	『博客源代码』需要使用Git做版本管理，而『博客生成代码』需要使用Git部署。因此容易混淆。
2.方法1：使用2个不同的Git仓库分别管理『博客源代码』和『博客生成代码』

	在GitHub新建一个仓库，然后将『博客源代码』同步到新项目。『博客生成代码』仍然由gladysgong/gladysgong.github.io部署。
3.方法2:使用同一个Git仓库的2个不同的Git分支分别管理『博客源代码』和『博客生成代码』

	修改Hexo的配置文件_config.yml，将『博客生成代码』部署到gladysgong/gladysgong.github.io仓库的develop分支:
	deploy:
		type: git
		branch: develop
		repo: https://github.com/gladysgong/gladysgong.github.com
	
### 九、bug
#### 1.hexo报错：

```
YAMLException: cannot read a block mapping entry; a multi line key may not be an implicit key at line 5, column 1: # Site

YAMLException: cannot read a block mapping entry; a multi line key may not be an implicit key at line 13, column 1: # URL
```
	配置文件：_config.yml  中 # Site #URL 属性设置后面的：需要有空格再填写内容！！！
#### 2.npm各种提示权限不够，windows下即使开了administrator也提示权限不够
	无奈，卸载npm，用淘宝npm镜像重装一个，安装后一路畅通。
	安装方法：npm install -g cnpm --registry=https://registry.npm.taobao.org
	参考链接：http://npm.taobao.org/
	之后所用命令只需改为cnpm即可
#### 3.hexo s运行时默认4000端口被占用
	尝试过解除端口，无奈我的4000端口被360浏览器占用
	hexo s -p 8022
	重启一个8022或者任意一个没被占用的端口即可


