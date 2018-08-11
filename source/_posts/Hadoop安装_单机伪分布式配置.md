---
title: Hadoop安装_单机伪分布式配置
date: 2017-08-16 20:34
tags: Hadoop 
---

# Hadoop安装_单机伪分布式配置
## 创建hadoop账户

添加用户：

    sudo useradd -m hadoop -s /bin/bash
给用户设置密码：

    sudo passwd hadoop
给用户管理员权限：

    sudo adduser hadoop sudo

更新apt

    sudo apt-get update
安装vim:

    sudo apt-get install vim
<!--more-->
## 安装SSH、配置SSH无密码登陆

    sudo apt-get install openssh-server
    ssh localhost
    exit                           # 退出刚才的 ssh localhost
    cd ~/.ssh/                     # 若没有该目录，请先执行一次ssh localhost
    ssh-keygen -t rsa              # 会有提示，都按回车就可以
    cat ./id_rsa.pub >> ./authorized_keys  # 加入授权
此时再   ssh localhost   时就可以无密码访问了。

## 安装Java环境

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

我们选择将 Hadoop 安装至 /usr/local/ 中：

    sudo tar -zxf ~/下载/hadoop-2.7.3.tar.gz -C /usr/local    # 解压到/usr/local中
    cd /usr/local/
    sudo mv ./hadoop-2.7.3/ ./hadoop            # 将文件夹名改为hadoop
    sudo chown -R hadoop ./hadoop       # 修改文件权限
**Hadoop 解压后即可使用。输入如下命令来检查 Hadoop 是否可用，成功则会显示 Hadoop 版本信息：**

    cd /usr/local/hadoop
    ./bin/hadoop version

## Hadoop单机配置(非分布式)
【这一步个人认为可以用来检测hadoop是否安装成功，可以不用管的】

    cd /usr/local/hadoop
    mkdir ./input
    cp ./etc/hadoop/*.xml ./input   # 将配置文件作为输入文件
    ./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep ./input ./output 'dfs[a-z.]+'  （和上面一行连着的）
    cat ./output/*          # 查看运行结果
    rm -r ./output

## Hadoop伪分布式配置

Hadoop 可以在单节点上以伪分布式的方式运行，Hadoop 进程以分离的 Java 进程来运行，节点既作为 NameNode 也作为 DataNode，同时，读取的是 HDFS 中的文件。

Hadoop 的配置文件位于 /usr/local/hadoop/etc/hadoop/ 中，伪分布式需要修改2个配置文件 core-site.xml 和 hdfs-site.xml 。Hadoop的配置文件是 xml 格式，每个配置以声明 property 的 name 和 value 的方式来实现。

修改配置文件 **core-site.xml** (通过 vim 编辑会比较方便: vim ./etc/hadoop/core-site.xml)，将当中的
```bash
<configuration>
</configuration>
修改为下面配置：
<configuration>
        <property>
             <name>hadoop.tmp.dir</name>
             <value>file:/usr/local/hadoop/tmp</value>
             <description>Abase for other temporary directories.</description>
        </property>
        <property>
             <name>fs.defaultFS</name>
             <value>hdfs://localhost:9000</value>
        </property>
</configuration>
```
同样的，修改配置文件 **hdfs-site.xml**：
```bash
<configuration>
        <property>
             <name>dfs.replication</name>
             <value>1</value>
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
***配置完成后，执行 NameNode 的格式化:
成功的话，会看到 “successfully formatted” 和 “Exitting with status 0” 的提示，若为 “Exitting with status 1” 则是出错。***

**接着开启 NameNode 和 DataNode 守护进程。**

    ./sbin/start-dfs.sh
**输入yes（可能第一次需要）**
启动完成后，可以通过命令 jps 来判断是否成功启动，若成功启动则会列出如下进程: “NameNode”、”DataNode” 和 “SecondaryNameNode”（如果 SecondaryNameNode 没有启动，请运行 sbin/stop-dfs.sh 关闭进程，然后再次尝试启动尝试）。如果没有 NameNode 或 DataNode ，那就是配置不成功，请仔细检查之前步骤，或通过查看启动日志排查原因。
**成功启动后，可以访问 Web 界面 [http://localhost:50070/](http://localhost:50070/ "") 查看 NameNode 和 Datanode 信息，还可以在线查看 HDFS 中的文件。**

## 运行Hadoop伪分布式实例
**（配置时其实也可以不用管，它只是跑了一个例子）**

上面的单机模式，grep 例子读取的是本地数据，伪分布式读取的则是 HDFS 上的数据。要使用 HDFS，首先需要在 HDFS 中创建用户目录：

    ./bin/hdfs dfs -mkdir -p /user/hadoop
    
接着将 ./etc/hadoop 中的 xml 文件作为输入文件复制到分布式文件系统中，即将 /usr/local/hadoop/etc/hadoop 复制到分布式文件系统中的 /user/hadoop/input 中。我们使用的是 hadoop 用户，并且已创建相应的用户目录 /user/hadoop ，因此在命令中就可以使用相对路径如 input，其对应的绝对路径就是 /user/hadoop/input:

    ./bin/hdfs dfs -mkdir input
    ./bin/hdfs dfs -put ./etc/hadoop/*.xml input
    
复制完成后，可以通过如下命令查看文件列表：

    ./bin/hdfs dfs -ls input
    
伪分布式运行 MapReduce 作业的方式跟单机模式相同，区别在于伪分布式读取的是HDFS中的文件（可以将单机步骤中创建的本地 input 文件夹，输出结果 output 文件夹都删掉来验证这一点）。

    ./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep input output 'dfs[a-z.]+'
    
查看运行结果的命令（查看的是位于 HDFS 中的输出结果）：

    ./bin/hdfs dfs -cat output/*
    
我们也可以将运行结果取回到本地：

    rm -r ./output    # 先删除本地的 output 文件夹（如果存在）
    ./bin/hdfs dfs -get output ./output     # 将 HDFS 上的 output 文件夹拷贝到本机
    cat ./output/*
    
Hadoop 运行程序时，输出目录不能存在，否则会提示错误 “org.apache.hadoop.mapred.FileAlreadyExistsException: Output directory hdfs://localhost:9000/user/hadoop/output already exists” ，因此若要再次执行，需要执行如下命令删除 output 文件夹:

    ./bin/hdfs dfs -rm -r output    # 删除 output 文件夹
    
若要关闭 Hadoop，则运行

    ./sbin/stop-dfs.sh

## 启动YARN

（伪分布式不启动 YARN 也可以，一般不会影响程序执行）

YARN 是从 MapReduce 中分离出来的，负责资源管理与任务调度。YARN 运行于 MapReduce 之上，提供了高可用性、高扩展性

**上述通过 ./sbin/start-dfs.sh 启动 Hadoop，仅仅是启动了 MapReduce 环境，我们可以启动 YARN ，让 YARN 来负责资源管理与任务调度。**

首先修改配置文件 mapred-site.xml，这边需要先进行重命名：

    mv ./etc/hadoop/mapred-site.xml.template ./etc/hadoop/mapred-site.xml
    
然后再进行编辑，同样使用 vim 编辑 **vim ./etc/hadoop/mapred-site.xml** ：
```bash
<configuration>
        <property>
             <name>mapreduce.framework.name</name>
             <value>yarn</value>
        </property>
</configuration>
```
接着修改配置文件 **yarn-site.xml**：
```bash
<configuration>
        <property>
             <name>yarn.nodemanager.aux-services</name>
             <value>mapreduce_shuffle</value>
            </property>
</configuration>
```
然后就可以启动 YARN 了（需要先执行过 ./sbin/start-dfs.sh）：

    ./sbin/start-yarn.sh      # 启动YARN
    ./sbin/mr-jobhistory-daemon.sh start historyserver  # 开启历史服务器，才能在Web中查看任务运行情况
    
开启后通过 **jps** 查看，可以看到多了 NodeManager 和 ResourceManager 两个后台进程
启动 YARN 之后，运行实例的方法还是一样的，仅仅是资源管理方式、任务调度不同。观察日志信息可以发现，不启用 YARN 时，是 “mapred.LocalJobRunner” 在跑任务，启用 YARN 之后，是 “mapred.YARNRunner” 在跑任务。启动 YARN 有个好处是可以通过 Web 界面查看任务的运行情况：[http://master:8088/cluster](http://master:8088/cluster "")，

同样的，关闭 YARN 的脚本如下：

    ./sbin/stop-yarn.sh
    ./sbin/mr-jobhistory-daemon.sh stop historyserver




# 参考资料

Hadoop集群安装配置教程_Hadoop2.6.0_Ubuntu/CentOS：
http://www.powerxing.com/install-hadoop-cluster/

Hadoop安装教程_单机/伪分布式配置_Hadoop2.6.0/Ubuntu14.04
http://www.powerxing.com/install-hadoop/
