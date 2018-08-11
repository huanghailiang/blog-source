---
title: Ubuntu server 16.04 安装教程------by邵碧尧
date: 2017-07-23 16:10:31
tags: 手把手教你配置Ubuntu server服务器
---

# 手把手教你配置Ubuntu server服务器

近期实验室来了台新的服务器，第一次从头到尾把配置过程走了一遍，特来此总结一番，希望可以帮助到想入坑的童鞋。我将从以下几个方面来总结配置服务器的过程(系统版本为Ubuntu server 16.04)

- **Ubuntu server 16.04 安装教程**
- **服务器多硬盘挂载**
- **设置服务器网络**
- **管理服务器用户**
- **安装远程图形界面*x2go***

-------------------

<!--more-->

## Ubuntu server 16.04 安装教程


-------------------


### Ubuntu server 16.04 启动U盘制作
用装系统，没启用U盘怎么行呢！Ubuntu系统的启动U盘制作非常简单，首先从[官网][1]下载对应版本的系统镜像文件，下一步你需要一个空白的U盘，用于写入镜像文件，如果U盘中有资料千万做好备份。写入镜像文件的软件可以使用[软碟通][2]。

用软碟通打开镜像文件

![这里写图片描述](http://img.blog.csdn.net/20170706160133736?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

菜单中“启动”->“写入硬盘映像”

![这里写图片描述](http://img.blog.csdn.net/20170706160043376?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

写入U盘中，点击“写入”，然后就可以吃着火锅唱着歌等着进度条跑完就好了

![这里写图片描述](http://img.blog.csdn.net/20170706160538657?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

###安装Ubuntu server
**Markdown　Extra**　表格语法：
首先用U盘启动，之前需要查下主板进入boot menu的快捷键（一般是Esc，Del，F2，F12等），选择自己U盘启动（可能会有一个UEFI选项，两者都行）

**2017.7.12 update**: 推荐大家安装时都选择英文版本，有时候选择中文版在最后安装的时候会报一些没办法解决的错误

用启动盘成功引导之后，出现下面的界面，恭喜你完成了第一步

![这里写图片描述](http://img.blog.csdn.net/20170706161239911?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

选择语言你喜欢什么语言就什么语言咯

![这里写图片描述](http://img.blog.csdn.net/20170706161432408?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

选择是否继续安装所选择的语言版本，选择”yes

![这里写图片描述](http://img.blog.csdn.net/20170706161619078?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

选择”否”，默认键盘模式就好了

![这里写图片描述](http://img.blog.csdn.net/20170706161824038?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170706162022884?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170706162043616?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

然后等进度条跑完，到这里键盘就已经好了，下面来配置服务器的一些基本信息。主机名自己起，但只能用字母和数字

![这里写图片描述](http://img.blog.csdn.net/20170706162337736?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

激动人心的时候到了，设置服务器的第一个用户，没错，就是你啦

![这里写图片描述](http://img.blog.csdn.net/20170706162712197?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

设置密码

![这里写图片描述](http://img.blog.csdn.net/20170706162730865?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

要不要加密主目录，先“否”吧

![这里写图片描述](http://img.blog.csdn.net/20170706162837668?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

设置时区，还是默认

![这里写图片描述](http://img.blog.csdn.net/20170706163002307?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

接下来就是最关键的，选择系统安装硬盘，作者小白就不用“手动”了

![这里写图片描述](http://img.blog.csdn.net/20170706163203372?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这里选择空白区（理想情况就是系统装在固态中，然后再挂载一个大的机械硬盘）

![这里写图片描述](http://img.blog.csdn.net/20170706163621240?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这里我是选了一块空白的固态，大小是“max”

![这里写图片描述](http://img.blog.csdn.net/20170706163748076?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

分区设定结束并将修改写入磁盘，结束后出现下面窗口

![这里写图片描述](http://img.blog.csdn.net/20170706164031657?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

用空格选择：OpenSSH server

![这里写图片描述](http://img.blog.csdn.net/20170706164143362?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

选择“yes”

![这里写图片描述](http://img.blog.csdn.net/20170706164247665?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

“continue”重启，完工

![这里写图片描述](http://img.blog.csdn.net/20170706164317696?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUm9nZXIxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

网络什么的安装过程不配置都没有问题，系统装好了再解决，放心大胆搞，最坏就是重装嘛，硬件一般不会搞坏。今天先写到这里，后面我接着按照前面的大纲更新，其实网上这些东西都有，我就是帮大家归纳总结下，希望对大家有帮助吧

---------

[1]: https://www.ubuntu.com/download/server
[2]: https://cn.ultraiso.net/xiazai.html
[3]: http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference
[4]: http://bramp.github.io/js-sequence-diagrams/
[5]: http://adrai.github.io/flowchart.js/
[6]: https://github.com/benweet/stackedit
