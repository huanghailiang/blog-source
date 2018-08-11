---
title: XX-net
date: 2017-11-29 13:31:56
tags: 工具的使用
---


# 使用XX-net进行上网

xx-net个人用它来上外网的。

<!--more-->

参考网址：https://github.com/XX-net/XX-Net

1、安装谷歌浏览器

2、下载或者使用git克隆xx-net软件； https://github.com/XX-net/XX-Net.git

3、将文件放置到希望安装的位置，然后运行 start.vbs即可。

完成上面的步骤，如果使用的是学校的网络，那么应该就可以直接上外网了，如果是自己寝室的网络需要根据上面的提示设置一下IPV6然后重新启动计算机即可。

这个时候使用的是公共appid，当然你也可以用你自己的谷歌账户创建自己的appid，一个谷歌账户可以创建12个appid.直接点配置文件重点的怎么申请即可。


## IPV6开启

参考网址：

https://github.com/XX-net/XX-Net/wiki/%E5%A6%82%E4%BD%95%E5%BC%80%E5%90%AFIPv6

设置如下：

  // 设置 Teredo 服务器，默认为：win10.ipv6.microsoft.com
  netsh interface teredo set state enterpriseclient server=default
  ping -6 ipv6.test-ipv6.com

  // 重置 IPv6 配置
  netsh interface ipv6 reset
