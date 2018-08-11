---
title: windows远程链接问题
date: 2018-05-11 13:49:18
tags: 运维
---

# windows远程链接问题

最近因为微软的更新我们在远程链接windows服务器的时候会出现如下问题：
<!--more-->
{% img /images/远程链接问题.jpg %}

使用微软官方建议修改本地组策略：计算机配置>管理模板>系统>凭据分配>加密Oracle修正 选择启用并选择易受攻击。

但是我们是win 10 家庭版没有组策略，我也尝试过网上添加的方式。如下：

1、打开txt复制以下代码：
```
@echo off

pushd "%~dp0"

dir /b C:\Windows\servicing\Packages\Microsoft-Windows-GroupPolicy-ClientExtensions-Package~3*.mum >List.txt

dir /b C:\Windows\servicing\Packages\Microsoft-Windows-GroupPolicy-ClientTools-Package~3*.mum >>List.txt

for /f %%i in ('findstr /i . List.txt 2^>nul') do dism /online /norestart /add-package:"C:\Windows\servicing\Packages\%%i"

pause
```
2、保存后，后缀改为cmd

3、管理员身份运行cmd文件

4、win+r弹出运行窗口，输入gpedit.msc,打开本地组策略

但是我们好像无法找到**加密Oracle修正**这一项。


# 解决方法一

我们没办法添加组策略，那么我最终选择就是卸载掉微软的更新。
在控制面板，卸载程序，左边找到产看已安装的更新，翻到最底下找到如下的更新卸载后重启电脑即可。【但是这种方法不好的是我们在关机后卸载掉的更新又会回来，这样很不方便】

{% img /images/删除文件.jpg %}

# 解决方法二(较好的方法)


1、运行regedit，打开注册表

2、找文件夹 路径：

计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\CredSSP\Parameters（正常情况下只会到System，需要手动添加后两项路径）

3、再在Parameters里面 新建 DWORD（32）位，文件名 “AllowEncryptionOracle” ，值 : 2.

4、保存后如未生效，重启计算机

即可开启桌面远程连接
