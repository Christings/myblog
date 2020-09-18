---
title: Mac终端ssh登录Linux提示cannot change locale(UTF_8)
date: 2018-06-19 10:15:26
tags: ssh
categories:
        - 工具篇
        - ssh
---
## 警告
    在Mac终端ssh登录远程Linux主机时，总是提示：
    -bash: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
    即使在Linux中添加了export LANG=en_US，报错依旧在。
## 原因
    这个警告是ssh服务器和终端客户程序共同导致吃的。
    本地ssh客户端发动LC_*环境变量给远程sshd服务器，即ssh将尝试在远程服务器上设置和本地系统（我的是high Sierra系统）一样的LC_*环境变量，这样就导致了登录Linux远程服务器出现警告。
## 解决方法（3种 ）
1.在ssh客户端关闭启动时设置本地环境变量

    终端 > 偏好设置 > 描述文件 > 高级 > 启动时设置locale环境变量（关闭它）

    但我用了这个方法还是没有生效，可能哪里配置还得改吧，而且重启机器后这个选项还是开着的，按理说是可以用的。
2.禁止OpenSSH客户端从OS X/Linux/Unix桌面发送LC_*变量

    $ vi /etc/ssh/ssh_config
    删除或注释以下配置：SendEnv LANG LC_*

    # Site-wide defaults for some commonly used options.  For a comprehensive
    # list of available options, their meanings and defaults, please see the
    # ssh_config(5) man page.

    # Apple:
     Host *
       SendEnv LANG LC_*
    #   AskPassGUI yes

    #   ForwardAgent no
    #   ForwardX11 no
    #   RhostsRSAAuthentication no
    #   RSAAuthentication yes
    #   PasswordAuthentication yes
    #   HostbasedAuthentication no
    #   GSSAPIAuthentication no
    #   GSSAPIDelegateCredentials no
    ......

    我使用这个后成功，感觉这个方法比较合适。
3.在远程服务器上安装要求的locale
    
    可以使用localedef安装需要的locale
    或者考虑在本地/远程系统中选择另外的locale
    $ localedef -i en_US -f UTF-8 en.US.UTF-8


