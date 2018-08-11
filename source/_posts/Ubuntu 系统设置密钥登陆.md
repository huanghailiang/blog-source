---
title: Ubuntu 系统设置密钥登陆
date: 2017-07-22 17:48:31
tags: Ubuntu
---



# Ubuntu 系统设置密钥登陆

本文主要参考文档
	http://www.downgle.com/zt/zqxouw0qwvstzrouysrloolz.html
	http://blog.csdn.net/kenhins/article/details/51585565
自己简单整理留作备份以备后期查看
<!--more-->
个人感觉用密钥登陆比用密码登陆安全很多，所以一旦生成密码之后一定不要将自己的密钥给别人，否者别人将很轻松的登陆你的账户，当然我发现如果你使用XShell远程连接ubuntu服务器的时候你可以再给密钥加上一层密码的。
## 密钥的生成
这一步如果你使用XShell的话其实很简单，在上面的工具中有一个“新建用户密钥生成向导”，进去你自己一定会的，这里就不多说了
#设置密钥登陆
首先需要修改一些配置文件

	vim /etc/ssh/sshd_config
进入sshd_config文件后需要更改几个地方

	 PubkeyAuthentication yes    #启用公告密钥配对认证方式 
	 AuthorizedKeysFile  %h/.ssh/authorized_keys    #设定PublicKey文件路径
	 RSAAuthentication yes  #允许RSA密钥
	 PasswordAuthentication no #禁止密码验证登录,如果启用的话,RSA认证登录就没有意义了
	 #禁用root账户登录，非必要，但为了安全性，请配置
	 PermitRootLogin no

其实我在配置的时候发现其中有些找了很多遍都没有找到，然后我就直接手动照着输入一遍也是可以的。

然后保存配置，重新启动服务

	service sshd restart
	
#配置密钥
由上面的配置文件我们可以看出来密钥放置的位置应该在用户根目录下的.ssh/authorized_keys中，如果系统本身就有authorize_keys，那么你就可以直接将你的公钥复制进来但是一般情况下刚装好的新机器没有这个目录，这就需要我们先生成一个(这里我们可以直接进入你要创建的账户去进行，可以使用指令 su 需要创建的用户名)

	ssh-keygen
输入该指令后一路回车即可
回头用指令

	ll

你可以发现在当前用户的更目录下就会生成一个 .ssh的文件夹,然后再进入这个文件夹操作

	cd ~/.ssh
	vim authorized_keys
把自己生成的公钥复制进来就可以了
然后保存，并且更改文件模式

	chmod 600 authorized_keys
### 注意：有些时候复制的时候可能不全，尤其前面缺少几个字母，这个需要自己检查一下，否则登陆会不成功的
