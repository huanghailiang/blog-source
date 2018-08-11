---
title: nvidia驱动挂掉
date: 2018-01-16 21:09:21
tags: Ubuntu
---

# nvidia驱动挂掉

linux系统中，在内核自动升级后英伟达的驱动有时候会找不到，以至于驱动用不了，装的CUDA等都找不到。
<!--more-->
# 解决方法

以下是自己的解决方法。

我们选择使用dkms这个包来对驱动进行管理。

在此之前我们应该先查看一下gcc和g++的版本，这个可能会影响我们的编译，***【同时也有可能时gcc和g++的版本被我们降低了才导致了驱动更新没有跟上】***我们要将这两个版本调到原来系统的版本（如果有多版本的话），当然gcc和g++应该处于同一个版本。调整gcc和g++的版本可以参考以前的博客[ubuntu环境下降低gcc和g++的版本](https://huanghailiang.github.io/2017/07/22/ubuntu-Reduce-the-version-gcc-g++/ "")


安装dkms
```bash
sudo apt install dkms
```

查看dkms的状态

```
dkms status
```

使用dkms挂载nvidia

```
dkms nvidia
```

使用dkms自动安装

```
sudo dkms autoinstall
```

本人使用以上的方法解决了因为内核更新导致的驱动版本没有跟上的问题。作为笔记供日后查看。


注：

安装内核
```
sudo apt install linux-headers-generic
```

查看错误信息
```
cat /var/lib/dkms/nvidia/375.26/build/make.log
```