---
title: hadoop集群安装配置教程
date: 2017-08-16 20:28
tags: Hadoop 
---
# hadoop集群安装

## 准备工作

Hadoop 集群的安装配置大致为如下流程:

    1、选定一台机器作为 Master
    2、在 Master 节点上配置 hadoop 用户、安装 SSH server、安装 Java 环境
    3、在 Master 节点上安装 Hadoop，并完成配置
    4、在其他 Slave 节点上配置 hadoop 用户、安装 SSH server、安装 Java 环境
    5、将 Master 节点上的 /usr/local/hadoop 目录复制到其他 Slave 节点上
    6、在 Master 节点上开启 Hadoop
<!--more-->
## 创建hadoop账户

添加用户：

    sudo useradd -m hadoop -s /bin/bash
给用户设置密码：

    sudo passwd hadoop
给用户管理员权限：

    sudo adduser hadoop sudo

## 更新apt
    sudo apt-get update
安装vim:

    sudo apt-get install vim

##安装Java环境

    sudo apt-get install openjdk-8-jre openjdk-8-jdk
    dpkg -L openjdk-8-jdk | grep '/bin/javac'
    vim ~/.bashrc
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
    source ~/.bashrc    # 使变量设置生效
    检查是否正确：
    echo $JAVA_HOME     # 检验变量值
    java -version
    $JAVA_HOME/bin/java -version  # 与直接执行 java -version 一样
如果设置正确的话，$JAVA_HOME/bin/java -version 会输出 java 的版本信息，且和 java -version 的输出结果一样

## 安装 Hadoop 2

Hadoop 2 可以通过 http://mirror.bit.edu.cn/apache/hadoop/common/ 或者 http://mirrors.cnnic.cn/apache/hadoop/common/ 下载，一般选择下载最新的稳定版本，即下载 “stable” 下的 hadoop-2.x.y.tar.gz 这个格式的文件，这是编译好的，另一个包含 src 的则是 Hadoop 源代码，需要进行编译才可使用。

### 我们选择将 Hadoop 安装至 /usr/local/ 中：
    sudo tar -zxf ~/下载/hadoop-2.7.3.tar.gz -C /usr/local    # 解压到/usr/local中
    cd /usr/local/
    sudo mv ./hadoop-2.7.3/ ./hadoop            # 将文件夹名改为hadoop
    sudo chown -R hadoop ./hadoop       # 修改文件权限
    Hadoop 解压后即可使用。输入如下命令来检查 Hadoop 是否可用，成功则会显示 Hadoop 版本信息：
    cd /usr/local/hadoop
    ./bin/hadoop version
## 网络配置
假设集群**所用的节点都位于同一个局域网**。

首先在 Master 节点上完成准备工作，并关闭 Hadoop (/usr/local/hadoop/sbin/stop-dfs.sh)，再进行后续集群配置。

为了便于区分，可以修改各个节点的主机名（在终端标题、命令行中可以看到主机名，以便区分）。在 Ubuntu中，我们在 Master 节点上执行如下命令修改主机名（即改为 Master，注意是区分大小写的）：

设置主机名：

    sudo vim /etc/hostname
设置IP

    sudo vim /etc/hosts
设置如下：

    192.168.1.121   Master
    192.168.1.122   Slave1
我们在 /etc/hosts 中将该映射关系填写上去即可，如下图所示（一般该文件中只有一个 127.0.0.1，其对应名为 localhost，如果有多余的应删除，特别是不能有 “127.0.0.1 Master” 这样的记录）

配置好后需要在各个节点上执行如下命令，测试是否相互 ping 得通，如果 ping 不通，后面就无法顺利配置成功：
ping Master -c 3   # 只ping 3次，否则要按 Ctrl+c 中断
ping Slave1 -c 3

**继续下一步配置前，请先完成所有节点的网络配置，修改过主机名的话需重启才能生效。**

## SSH无密码登陆节点

    sudo apt-get install openssh-server

**Master上面操作：**

首先生成 Master 节点的公匙，在 Master 节点的终端中执行（因为改过主机名，所以还需要删掉原有的再重新生成一次）：

    cd ~/.ssh               # 如果没有该目录，先执行一次ssh localhost
    rm ./id_rsa*            # 删除之前生成的公匙（如果有）
    ssh-keygen -t rsa       # 一直按回车就可以
让 Master 节点需能无密码 SSH 本机，在 Master 节点上执行：

    cat ./id_rsa.pub >> ./authorized_keys    
完成后可执行 ssh Master 验证一下（可能需要输入 yes，成功后执行 exit 返回原来的终端）。接着在 Master 节点将上公匙传输到 Slave1 节点：（当然多个节点的话，每个节点都要传输）

    scp ~/.ssh/id_rsa.pub hadoop@Slave1:/home/hadoop/

**Slave1上面操作：**

接着在 Slave1 节点上，将 ssh 公匙加入授权：

    mkdir ~/.ssh       # 如果不存在该文件夹需先创建，若已存在则忽略
    cat ~/id_rsa.pub >> ~/.ssh/authorized_keys
    rm ~/id_rsa.pub    # 用完就可以删掉了
    
如果有其他 Slave 节点，也要执行将 Master 公匙传输到 Slave 节点、在 Slave 节点上加入授权这两步。
（如果配置好了以后可以用 ssh Slave1 指令检验，发现可以不用密码就可以连接上了）

### 配置PATH变量

在单机伪分布式配置教程的最后，说到可以将 Hadoop 安装目录加入 PATH 变量中，这样就可以在任意目录中直接使用 hadoo、hdfs 等命令了，如果还没有配置的，需要在 Master 节点上进行配置。首先执行 vim ~/.bashrc，加入一行：

    export PATH=$PATH:/usr/local/hadoop/bin:/usr/local/hadoop/sbin
保存后执行 

    source ~/.bashrc 
使配置生效

### 配置集群/分布式环境

集群/分布式模式需要修改 ***/usr/local/hadoop/etc/hadoop*** 中的5个配置文件，更多设置项可点击查看官方说明，这里仅设置了正常启动所必须的设置项： slaves、core-site.xml、hdfs-site.xml、mapred-site.xml、yarn-site.xml 。

***1,*** 文件 slaves，将作为 DataNode 的主机名写入该文件，每行一个，默认为 localhost，所以在伪分布式配置时，节点即作为 NameNode 也作为 DataNode。分布式配置可以保留 localhost，也可以删掉，让 Master 节点仅作为 NameNode 使用。

本教程让 Master 节点仅作为 NameNode 使用，因此***将文件中原来的 localhost 删除，只添加一行内容：Slave1。（相应的如果有多个节点要都添加上的）***

***2,*** 文件 ***core-site.xml*** 改为下面的配置：
```java
<configuration>
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://Master:9000</value>
        </property>
        <property>
                <name>hadoop.tmp.dir</name>
                <value>file:/usr/local/hadoop/tmp</value>
                <description>Abase for other temporary directories.</description>
        </property>
</configuration>
```
***3,*** 文件 ***hdfs-site.xml***，dfs.replication 一般设为 3，但我们只有一个 Slave 节点，所以 dfs.replication 的值还是设为 1：（如果是多个节点还是改为3）【这里的参数我的理解是数据备份多少次】
```java
<configuration>
        <property>
                <name>dfs.namenode.secondary.http-address</name>
                <value>Slave0:50090</value>
        </property>
        <property>
                <name>dfs.replication</name>
                <value>3</value>
        </property>
        <property>
                <name>dfs.namenode.name.dir</name>
                <value>file:/usr/local/hadoop/tmp/dfs/name</value>
        </property>
        <property>
                <name>dfs.datanode.data.dir</name>
                <value>file:/usr/local/hadoop/tmp/dfs/data</value>
        </property>
</configuration>
```
***4, ***文件 ***mapred-site.xml*** （可能需要先重命名，默认文件名为 mapred-site.xml.template），然后配置修改如下：
```java
<configuration>
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
        <property>
                <name>mapreduce.jobhistory.address</name>
                <value>Master:10020</value>
        </property>
        <property>
                <name>mapreduce.jobhistory.webapp.address</name>
                <value>Master:19888</value>
        </property>
</configuration>
```
***5,*** 文件 yarn-site.xml：
```java
<configuration>
        <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>Master</value>
        </property>
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>
</configuration>
```
配置好后，将 Master 上的 /usr/local/Hadoop 文件夹复制到各个节点上。因为之前有跑过伪分布式模式，建议在切换到集群模式前先删除之前的临时文件。

***在 Master 节点上执行：***

    cd /usr/local
    sudo rm -r ./hadoop/tmp     # 删除 Hadoop 临时文件
    sudo rm -r ./hadoop/logs/*   # 删除日志文件
    tar -zcf ~/hadoop.master.tar.gz ./hadoop   # 先压缩再复制
    cd ~
    scp ./hadoop.master.tar.gz Slave1:/home/hadoop

***在 Slave1 节点上执行：***（如果有多个节点每个节点上都要执行一遍）

    sudo rm -r /usr/local/hadoop    # 删掉旧的（如果存在）
    sudo tar -zxf ~/hadoop.master.tar.gz -C /usr/local
    sudo chown -R hadoop /usr/local/hadoop
（如果启动不成功的话可以删除  /usr/loval  目录下的 logs和 tmp 再重新操作一次）
同样，如果有其他 Slave 节点，也要执行将 hadoop.master.tar.gz 传输到 Slave 节点、在 Slave 节点解压文件的操作。

***首次启动需要先在 Master 节点执行 NameNode 的格式化：***

    hdfs namenode -format       # 首次运行需要执行初始化，之后不需要

接着可以启动 hadoop 了，启动需要在 Master 节点上进行：

    start-dfs.sh
    start-yarn.sh
    mr-jobhistory-daemon.sh start historyserver
    也可以在更目录下面执行
    start-all.sh
    mr-jobhistory-daemon.sh start historyserver
    
通过命令 jps 可以查看各个节点所启动的进程。
正确的话，在 Master 节点上可以看到 NameNode、ResourceManager、SecondrryNameNode、JobHistoryServer 进程

在 **Slave** 节点可以看到 DataNode 和 NodeManager 进程.

缺少任一进程都表示出错。另外还需要在 Master 节点上通过命令 hdfs dfsadmin -report 查看 DataNode 是否正常启动，如果 Live datanodes 不为 0 ，则说明集群启动成功。

也可以通过 Web 页面看到查看 DataNode 和 NameNode 的状态：[http://master:50070/](http://master:50070/ "")。如果不成功，可以通过启动日志排查原因。

同样可以通过 Web 界面查看任务进度 [http://master:8088/cluster](http://master:8088/cluster "")，在 Web 界面点击 “Tracking UI” 这一列的 History 连接，可以看到任务的运行信息

# 参考资料

Hadoop集群安装配置教程_Hadoop2.6.0_Ubuntu/CentOS：
http://www.powerxing.com/install-hadoop-cluster/

Hadoop安装教程_单机/伪分布式配置_Hadoop2.6.0/Ubuntu14.04
http://www.powerxing.com/install-hadoop/
