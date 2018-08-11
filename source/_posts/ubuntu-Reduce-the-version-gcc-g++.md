---
title: ubuntu环境下降低gcc和g++的版本
date: 2017-07-22 17:45:31
tags: Ubuntu
---


有时候我们在跑别人的程序的时候发现自己编译器的版本过高以至于不能运行，这个时候我们就需要降低编译器的版本。
# 首先安装低版本的编译器
这里由于本人用的ubuntu16.04所以编译器自带的是gcc-5.4和g++-5.4,但是需要低版本的，经过测试现在还可以安装4.7的版本

	sudo apt-get install gcc-4.7 gcc-4.7-multilib g++-4.7 g++-4.7-multilib
<!--more-->
# 配置级别
	sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.7 50
	sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 40
接着输入

	sudo update-alternatives --config gcc
然后可以自己选择版本。

同理配置好了gcc还要配置g++

	sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.7 50
	sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-5 40
同理g++的选择为：

	sudo update-alternatives --config g++

如果想删除可选项的话可以键入以下指令：

	sudo update-alternatives --remove gcc /usr/bin/gcc-4.5


本文是自己跟同门根据网上资料整理而得，希望对大家有用。
