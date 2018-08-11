---
title: 所有节点 文件拷贝脚本编写
date: 2017-08-17 13:46:18
tags: Hadoop脚本编写
---
# 所有节点 文件拷贝脚本编写
该脚本旨在往每个节点拷贝数据，之前我们的拷贝是需要自己一个节点一个节点链接然后拷贝，但是如果我们每个节点都拷贝同样的数据时，这样做显然很麻烦，如果节点多的话还很浪费时间，这样我们就可以通过该脚本自己循环拷贝。

<!--more-->
# xcp.sh源代码例子
```bash
#!/bin/bash
if [ $# -lt 1 ] ; then
echo no args
exit;
fi

#get first argument
arg1=$1;
cuser=`whoami`
fname=`basename $arg1`
dir=`dirname $arg1`
if [ $dir == "." ] ; then
dir=`pwd`
#	echo $dir
fi
for (( i=200;i<=500;i=i+100)) ;
do
echo -----------coping $arg1 to $i------------------;
if [ -d $arg1 ] ; then
scp  -r  $arg1 $cuser@s$i:$dir
else
scp $arg1 $cuser@s$i:$dir
fi
echo
done
```