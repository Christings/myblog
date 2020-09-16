---
title: Mac下使用iTerm2连接远程主机
date: 2018-08-31 11:33:11
tags: [SSH,iTerm2]
category:
		- Linux
		- SSH
---
## 步骤：
	1. 打开一个 iTerm2 窗口，选择菜单 profiles 中的 Open Profiles，然后选择 Edit Profiles 中创建一个新的 Profile，自己命名
	2. 选择 command，输入 ssh -A -p xxx root@输入远程计算机的 IP 地址（xxx 表示端口）。
	3. 然后选择这个新的 profile，New 一个 window 就可以连接远程服务器了。

