---
title: 管理服务器用户------by邵碧尧
date: 2017-07-23 16:16:31
tags: 手把手教你配置Ubuntu server服务器
---


# 手把手教你配置Ubuntu server服务器
## 管理服务器用户
管理服务器用户，一项一项开始说，首先要管理，当然要现有用户咯。添加用户我是用adduser 命令，相当于一个简单的交互过程，命令如下：

```
sudo adduser username
```
<!--more-->
然后会让你确定用户名，用户密码这些信息，结束后在/home目录下就有一个对应用户名的文件夹了。
管理员添加用户后，用户自己可以通过

```
passwd
```
来更新自己的密码。

哪天可能那个用户惹到root管理员了，管理员要删掉那个用户，怎么操作，非常简单

```
userdel username
```
秒删好吧！所以不要惹管理员

如果想修改用户名有改怎么办，有几种方案

第一种，简单粗暴，按照上面的方法删掉用户重新建

第二种，优雅一点，两条命令搞定

```
usermod -l New_Login_Name, -md Home_Directory Old_Login_Name 
groupmod -n NewName OldName
```

管理员会给服务器装一些基本的软件，有的用户想装自己需要的软件，但是没有权限怎么办嘞？每次都找管理员也是烦的，因此可以给部分用户 sudo 权限，怎么操作呢？
修改/etc/sudoers文件，在 root ALL=(ALL) ALL下方添加要给权限的用户名

```
root ALL=(ALL) ALL
username ALL=(ALL) ALL
```

还有种方法就是直接创建管理者账号

```
sudo adduser username sudo
```

以上就是一些最基本的用户管理指令，刚开始是够用了，到后面反正慢慢积累嘛
