---
title: 设置服务器网络------by邵碧尧
date: 2017-07-23 16:15:31
tags: 手把手教你配置Ubuntu server服务器
---


# 手把手教你配置Ubuntu server服务器

## 设置服务器网络
之前两篇讲了服务器系统安装和硬盘挂载，这篇总结下网络设置。具体开始讲之前先说说如何实现其他人可以访问服务器。一种方法是由一个网口出来接路由器，用户都连接到路由器的网络上，不管是通过有线还是WiFi，把用户都归到同一个网络下。另一种方法就比较bug了，像清华大学那样，整个学校有线网和无线的Tsinghua都是同一个网络，所以就不需要路由器了，之前不知道还倒腾了很久。又有人要问了，那如何让外网的人可以访问服务器呢？不好意思，我没有研究过，另一个原因也是担心网络安全的问题，毕竟没有学过相关的知识，所以搭个服务器内部用就完了。如果有大神在这方面比较有研究，我也非常欢迎交流。（邮箱：shaobiyao@gmail.com）
<!--more-->
说回来，先解决基本问题再说

sudo vim /etc/network/interfaces

修改如下部分：

auto eth0（这个名称每台机器不一样，可以通过ifconfig -a查询）
iface eth0 inet static
address 192.168.0.117 #选用同一个网络下不冲突的ip
gateway 192.168.0.1 #gateway and netmask 可以通过查看同一网络下的Windows电脑的信息获得，都一样的
netmask 255.255.255.0

sudo vim /etc/resolvconf/resolv.conf.d/base(网上有说修改/etc/resolv.conf的，这个文件修改完会被系统覆盖，等于白改)
nameserver XXX.XXX.XXX.XXX #和同一网络中Windows的电脑一样

重启网卡：

sudo /etc/init.d/network restart

用ping www.baidu.com测试是否成功
