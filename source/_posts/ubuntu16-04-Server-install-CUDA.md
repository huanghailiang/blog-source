---
title: ubuntu16.04 Server install CUDA
date: 2017-07-22 17:27:31
tags: Ubuntu
---

# ubuntu16.04 Server 安装CUDA

在安装CUDA的时候经常会遇到CUDA安装失败的情况，一般遇到的不允许第三方软件的安装，或者是自带的驱动nouveau的存在。
## 允许第三方软件的安装
这个需要自己重启电脑进入bios系统中去关闭UEFI的安全启动/安全引导
## 关闭自带的驱动nouveau
	sudo vim /etc/modprobe.d/blacklist.conf
在该文件中添加以下几行：

	blacklist vga16fb 
	blacklist rivafb 
	blacklist rivatv 
	blacklist nvidiafb
	options nouveau modeset=0
添加完以后保存该文件
接着更新该文件，使配置有用

	sudo update-initramfs -u
	
最后查看nouveau驱动禁止是否成功，如果没有内容则说明禁用成功。
### 如果nouveau驱动禁用后没有效果，可以进行强制移除该驱动（如果上面的方法禁用成功可以不使用该方法）
将这个驱动备份出来然后移除

	mv /lib/modules/3.0.0-12-generic/kernel/drivers/gpu/drm/nouveau/nouveau.ko /lib/modules/3.0.0-12-generic/kernel/drivers/gpu/drm/nouveau/nouveau.ko.org
重新加载

	 update-initramfs -u
重启发现字体已经变大了，卸载成功。
## CUDA的安装
因为本文说的ubuntu Server所以安装完一般使没有图形界面的，所以不需要去禁用图形界面，如果不是server版本的话一般还需要禁用图形界面的（可以用ctrl+alt+F1进入文本界面，F1~F6都可以F7是退出文本界面）。
禁用图形界面的语句：

	sudo service lightdm stop
打开图形界面的语句：

	sudo service lightdm stop
	
nouveau禁用成功了，bios中的UEFI也已经关了，这个时候就可以安心的装CUDA了。
首先需要安装依赖项：

	sudo apt-get install build-essential pkg-config linux-headers-$(uname -r)
	或者下面的方法也可以（用了上面的语句下面的语句就不需要了）：
	sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler 
	sudo apt-get install --no-install-recommends libboost-all-dev
	sudo apt-get install libopenblas-dev liblapack-dev libatlas-base-dev
	sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
	
下载CUDA的安装包
<https://developer.nvidia.com/cuda-downloads>
可以去NVIDIA的官网去下载，可以根据需求下载。
本文下载的runfile，其他形式的文本运行方法在下载界面有介绍的。
下载好后用U盘或者SCP等上传到服务器上，接下来进入cuda安装包的地方执行，当然这里根据你的安装包的型号。
	
	sudo sh cuda_8.0.61_375.26_linux.run
执行以后会有一堆文本让你读，很多，不过可以直接按q退出，然后按照要求先accept，然后再按照自己的需求安装，因为上面没有手动安装过nvidia的驱动，所以这里需要选择安装驱动的，否者cuda即使安装上了也没有用的。选择完了就开始安装了，安装完成后会有一个Summary显示安装的情况的。
![这里写图片描述](http://img.blog.csdn.net/20170715165538644?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFycmVuODE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
到此基本安装完了，可以使用下面的语句去检测nvidia驱动是否安装成功：
	
	cat /proc/driver/nvidia/version
如果可以弹出来驱动的版本信息等，说明安装成功，否者没有安装成功。

安装完后此时查找cuda是找不到的，因为还没有配置环境变量。
接下来配置环境变量：

	echo 'export PATH=/usr/local/cuda/bin:$PATH' >> ~/.bashrc
	echo 'export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
	source ~/.bashrc
配置完环境变量后再检查一下cuda是否安装成功

	nvcc -V
提示上面的必须是大写的V，小写的v是不能够被识别的。

##如果要卸载或者重装
卸载或者重新安装了。这个时候需要先卸载之前安装的。这个在用run文件安装的时候，最后那个summary已经告诉我们如何卸载了。

	To uninstall the CUDA Toolkit. run the uninstall scrip in /usr/local/cuda-8.0/bin
	To uninstall the NVIDIA Driver. run nvidia-unistall
可以先按照上面的提示去卸载CUDA和NVIDIA驱动，当然还有其他的方法
	
	sudo apt-get --purge remove <package_name>



### 本文参考网络上各种资源自己配置完自己实验室电脑后整理所得，做一个备忘所用，当然也希望对大家有用。