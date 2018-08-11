---
title: ubuntu密钥登录教程
date: 2017-07-22 17:48:31
tags: Ubuntu
---


# ubuntu密钥登录教程

## 上文已经讲诉了ubuntu系统设置密匙登陆，接下来讲解一下如何登陆。

### 本文使用XShell进行远程连接
### 使用WinSCP来进行ubuntu与ubuntu之间的文件的传输
<!--more-->
# XShell
首先现在登录时输入密码那一栏已经变成了灰色，这个时候只能使用密匙登录了。
![这里写图片描述](http://img.blog.csdn.net/20170716230052647?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFycmVuODE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
拿到密钥的第一步可以先添加密钥
![这里写图片描述](http://img.blog.csdn.net/20170716230143712?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFycmVuODE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170716230209146?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFycmVuODE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

找到自己密钥的路径即可
![这里写图片描述](http://img.blog.csdn.net/20170716230239310?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFycmVuODE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

密钥导入进来以后那个文件就可以不要了，当然如果还有其他地方需要的话还需要导入其他的地方，不过如果误删了不要方，旁边还有导出呢。
导入成功以后可以进入属性
![这里写图片描述](http://img.blog.csdn.net/20170716230314907?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFycmVuODE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

大家可以更改密码，初始密码都为空的，这个地方相当于你再给你的密钥又手动加了一层密码，当然也可以不加，因为我发现加了以后登录的时候还要你自己输入一遍。
属性上面还有一公钥，这个里面就是我们放到服务器中的东西，这个东西不要给别人，当然我给你们的密匙你们更不能给别人，否者别人就可以轻松的登录你的账号了。
剩下的登录方法更以前一样。

# WinSCP
同样此时winscp用密码也登录不上了
![这里写图片描述](http://img.blog.csdn.net/20170716230438986?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFycmVuODE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
确认

直接在之前建立的那个里面更改就好，点击编辑
![这里写图片描述](http://img.blog.csdn.net/20170716230513915?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFycmVuODE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
然后点高级
![这里写图片描述](http://img.blog.csdn.net/20170716230529320?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFycmVuODE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

验证
![这里写图片描述](http://img.blog.csdn.net/20170716230551613?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFycmVuODE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

然后添加密钥文件的路径
![这里写图片描述](http://img.blog.csdn.net/20170716230616590?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFycmVuODE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

右下角选择所有文件
![这里写图片描述](http://img.blog.csdn.net/20170716230644369?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFycmVuODE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

选择密匙
然后有一个格式转换，选择确定
![这里写图片描述](http://img.blog.csdn.net/20170716230702329?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFycmVuODE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

然后保存确定保存就完了，大功告成可以链接了。
