---
title: 安装远程图形界面x2go------by邵碧尧
date: 2017-07-23 16:25:31
tags: 手把手教你配置Ubuntu server服务器
---

# 手把手教你配置Ubuntu server服务器
## 安装远程图形界面x2go

Ubuntu server 安装完成只有命令行界面，一般的工作在Windows下用 [xshell][1] 和 [winscp][2] ，一个输命令行，一个进行界面化文件管理，还是挺方便的， 但部分用户还是希望有个图形界面更加方便科研工作开展，这里推荐x2go这个轻量化桌面，安装也比较方便，下面就来介绍。
<!--more-->
首先在服务器上安装一些必要的软件

```
  sudo apt-get install software-properties-common
  sudo add-apt-repository ppa:x2go/stable
  sudo apt-get update
  sudo apt-get install x2goserver x2goserver-xsession
  sudo apt-get update
  sudo apt-get install -y xfce4 xfce4-goodies xubuntu-desktop
```

很简单，几条命令结束就OK了。结束用

```
hostname -I
```
查看ip，后面在Windows下要用到

然后在 Windows 或者 OS X 下安装一个客户端 [x2go client][3] ,安装结束打开会跳出如下窗口

![这里写图片描述](http://img.blog.csdn.net/20170707213101276?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

在"Host" 中填写服务器ip
“Login”中填写要登录的username
“Session type”下拉菜单选择“XFCE”，点击OK结束。

![这里写图片描述](http://img.blog.csdn.net/20170707213552457?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

在这里输入密码就可以远程连接服务器，同时拥有图形界面了。

配置服务器系列到这里就结束了，小白我自己也在后面多多积累相关的技巧，有了新的经验再来更新，希望大家支持！

[1]:https://www.netsarang.com/products/xsh_overview.html
[2]:https://winscp.net/eng/index.php
[3]:http://wiki.x2go.org/doku.php/download:start
