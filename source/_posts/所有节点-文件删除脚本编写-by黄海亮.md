---
title: 所有节点 文件删除脚本编写
date: 2017-08-17 13:49:47
tags: Hadoop脚本编写
---
# 所有节点 文件删除脚本编写
该脚本旨在删除所有节点上相同的东西，比如hadoop的重新配置等，还有删除hadoop的log等文件，都比较方便。
<!--more-->
# xrm.sh的源代码例子
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
echo -----------------rming $arg1 from localhost-------------;
rm -rf $arg1
echo

for (( i=200;i<=500;i=i+100)) ;
do
echo ---------------rming $arg1 from s$i -------------;
ssh s$i rm -rf $dir/$fname
echo
done
```