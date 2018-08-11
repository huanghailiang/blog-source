---
title: 服务器多硬盘挂载------by邵碧尧
date: 2017-07-23 16:13:31
tags: 手把手教你配置Ubuntu server服务器
---

# 手把手教你配置Ubuntu server服务器

## 服务器多硬盘挂载

现在科研中需要处理的数据量经常性会非常大，因此服务器基本都会采用固态硬盘加机械硬盘的解决方案来实现服务器性能和成本的平衡，系统装在固态中，用户数据和数据集存放在机械硬盘中。接下来就讲下如何挂载硬盘

参考网页：

http://zwkufo.blog.163.com/blog/static/258825120141283942244/
http://www.jianshu.com/p/5c12289dbcd1
<!--more-->
sudo fdisk –lu (查看当前硬盘及分区情况) 可以看到类似界面

 ![这里写图片描述](http://img.blog.csdn.net/20170707140012854?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

列表中我们可以看到所有硬盘，找到要挂载的硬盘名称，例如/dev/sdb

2.	sudo fdisk /dev/sdb(这里为想要挂载的硬盘名称)，利用该命令可以对硬盘进行分区，具体可以参考网页1中的讲解更加详细

3.	sudo mkfs –t ext4 /dev/sdb(硬盘格式化，-t ext4 表示将分区格式化成ext4文件系统类型)

4.	sudo df –l(显示硬盘挂载情况) 
	sudo mount –t ext4 /dev/sdb(想要挂载的硬盘) /devdata(目标挂载点)
结束后可以用sudo df –l 查看是否成功
5. 配置系统启动自动挂载，修改/etc/fstab文件，添加
   # /devdata was on /dev/sdb
UUID=37eaa526-5d96-4237-8468-603df5216ce9  /devdata(挂载点)   ext4   defaults     0     2
硬盘的UUID通过命令 sudo blkid 进行查看

这样就把/dev/sdb挂载到/devdata这个文件夹下了。有人要问了，我不要挂这里，要把硬盘挂到/home目录下怎么办？接下来就是解决这个问题

首先要做的是卸载原先的挂载点，怎么做，很简单， sudo umount /dev/sdb(这里好像是写挂载的文件夹名也可以，但我没有试过)，再用 sudo df -l 查看，原先挂载的硬盘就没有了。

接下来你得挂载已经分区好的硬盘，然后把 home 目录下的全部文件拷贝到硬盘挂载的目录下。然后删除 home 目录，最后把第一步挂载好的新硬盘重新挂载在 home 目录下。

挂载设置好的硬盘

![这里写图片描述](http://img.blog.csdn.net/20170707142035471?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

同步 home 目录所有文件，删除之前的 home 目录下的所有文件

![这里写图片描述](http://img.blog.csdn.net/20170707142632300?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

确定同步成功之后，删除旧 home 目录

![这里写图片描述](http://img.blog.csdn.net/20170707142723858?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

重新挂载新硬盘并设置启动挂载

![这里写图片描述](http://img.blog.csdn.net/20170707142916104?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这里如果失败的话就修改/etc/fstab文件，把原先的挂载点改成/home
再 sudo df -l，就可以看到成功挂载到/home下了，不过这样对新机器是没什么影响，如果原先机器上有非root用户，这样操作后就GG了，还是要做好数据备份
