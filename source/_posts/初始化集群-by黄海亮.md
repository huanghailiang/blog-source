---
title: 初始化集群
date: 2017-08-17 13:52:50
tags: Hadoop脚本编写
---
# 初始化集群

该脚本主要时对第一次使用集群的时候进行一个初始化。

<!--more-->
# init.sh源代码例子
```bash
#!/bin/bash
 hdfs namenode -format
start-all.sh
```
或者
```bash
start-dfs.sh
start-yarn.sh
```
# 创建目录
```bash
./bin/hdfs dfs -mkdir -p /user/hadoop
```
# 查看显示目录
```bash
hadoop fs –ls -R /
```