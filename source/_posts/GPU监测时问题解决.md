---
title: GPU监测时问题解决
date: 2017-10-19 12:56:00
tags: Ubuntu
---

# GPU监测时问题解决
不管在我们使用服务器还是使用自己的桌面版本的时候，跑程序时一方面，有时候我们会监测一下电脑的资源使用情况,但是监测的时候我们可以能会遇到一些问题。
<!--more-->
对于CPU资源的使用，我们可以使用top或者使用更高级的htop指令进行监测，这个里面包含了很多的信息。
对于GPU资源的使用，我们一般使用nvidia-smi来进行一个监测，因为该指令是一个静态的，所以如果我们想动态的监测GPU的使用情况，我们可以配合watch指令进行使用，watch -n 1 nvidia-smi.

以上是我们使用一些基本方法，但是我在监测GPU的时候，可能会遇到以下两个问题：

1、GPU明明没有在用，但是显示的时候却显示GPU的利用率却很高，但是再看功率的时候又可以肯定GPU并没有在使用；

2、当一个用户用GPU跑完程序后，关掉了程序，但是GPU依旧显示该程序占用内存。

# 解决方法

针对以上的两个问题，本人的解决方法如下

## GPU监测时显示不准确

对于GPU监测显示不准确时的指令如下：
```bash
sudo nvidia-smi -pm 1
```

## GPU内存没有清零

对于程序结束了，但是GPU内存没有被清零的情况，解决方案如下：

使用nvidia-smi指令进行查找没有释放内存的程序的PID；
然后使用如下指令进行释放：
```bash
sudo kill -9 PID
```

# 参考资料
https://serverfault.com/questions/809038/why-is-my-cuda-gpu-util-70-when-there-are-no-running-processes-found

http://blog.csdn.net/lgh0824/article/details/77096241