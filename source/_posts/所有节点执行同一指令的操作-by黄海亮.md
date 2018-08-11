---
title: 所有节点执行同一指令的操作
date: 2017-08-17 13:43:26
tags: Hadoop脚本编写
---
# 所有节点执行同一指令的操作
该脚本旨在对每个节点都执行同一个指令，这样就不用每次都shell到每个子节点上去执行了，较为方便。
<!--more-->
# xcall.sh的源代码例子
```bash
#!/bin/bash
if [ $# -lt 1 ] ; then
echo no args
exit;
fi
echo -----------------xcall $arg1 from localhost-----------------
$@
echo
#get first argument
for (( i=0;i<=3;i=i+1));
do 
echo --------------------xcall $arg1 from Slave$i ------------------;
ssh Slave$i $@
echo
done
```