---
title: 清理集群的脚本
date: 2017-08-17 13:56:08
tags: Hadoop脚本编写
---
# 清理集群的脚本
该脚本旨在重新配置hadoop的时候我们需要清理一下集群中的东西，比如记录等。

<!--more-->
# clear.sh源代码例子
```bash
#!/bin/bash
stop-all.sh
xrm.sh /tmp/hadoop
xrm.sh /hadoop/logs
echo "clean done!"
(主要为了清除tmp下面的文件hadoop还有logs)
```