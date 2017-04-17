---
title: Linux上安装zookeeper(单机模式)  
date: 2017-04-17  
tags: zookeeper
---

&nbsp;&nbsp;&nbsp; Zookeeper作为Dubbo服务的注册中心，Dubbo原先基于数据库的注册中心，没采用Zookeeper，Zookeeper一个分布式的服务框架，是树型的目录服务的数据存储，能做到集群管理数据 ，这里能很好的作为Dubbo服务的注册中心，Dubbo能与Zookeeper做到集群部署，当提供者出现断电等异常停机时，Zookeeper注册中心能自动删除提供者信息，当提供者重启时，能自动恢复注册数据，以及订阅请求。我们先在linux上安装Zookeeper，我们安装最简单的单点，集群比较麻烦。  
&nbsp;&nbsp;&nbsp; ZooKeeper的安装模式分为三种，分别为：单机模式（stand-alone）、集群模式和集群伪分布模式。ZooKeeper 单机模式的安装相对比较简单，此篇就以单机模式为示例
#### 准备工作
Zookeeper要求JAVA的环境才能运行，并且需要jdk1.6以上。  
下载地址: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

``` bash
$ mkdir usr/java
$ wget jdk下载链接
$ tar -zxvf jdk-8u121-linux-x64.tar.gz\?AuthParam\=1492407600_1f244cbec307bf99f60713c6680fbe04
$ mv jdk-8u121-linux-x64.tar.gz\?AuthParam\=1492407600_1f244cbec307bf99f60713c6680fbe04 jdk1.8
$ vim /etc/profile
> JAVA_HOME=/usr/java/jdk1.8
> JRE_HOME=$JAVA_HOME/jre
> PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
> CLASSPATH=CLASSPATH=:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib/dt.jar
> export JAVA_HOME JRE_HOME PATH CLASSPATH
$ source /etc/profile
$ java -version
```
显示jdk版本，说明安装成功

#### 下载zookeeper源码
地址：http://www.apache.org/dist/zookeeper/
``` bash
$ wget http://www.apache.org/dist/zookeeper/zookeeper-3.4.10/zookeeper-3.4.10.tar.gz
$ tar -zxvf zookeeper-3.4.10.tar.gz
```

<!--more-->
在对应的zookeeper-3.4.6/conf 下有一个文件zoo_sample.cfg的这个文件里面配置了监听客户端连接的端口等一些信息，Zookeeper 在启动时会找zoo.cfg这个文件作为默认配置文件,所以我们复制一个名称为zoo.cfg的文件
``` bash
$ cd zookeeper-3.4.10
$ cd conf
$ cp zoo_sample.cfg zoo.cfg
$ cat zoo.cfg
```

``` 
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just 
# example sakes.
dataDir=/tmp/zookeeper
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the 
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1
```
参数说明:  
tickTime: 基本事件单元，以毫秒为单位。用来控制心跳和超时，默认情况下最小的会话超时时间为两倍的tickTime。  
dataDir: 存储内存中数据库快照的位置，如果不设置参数，更新事务日志将被存储到默认位置。  
clientPort: 监听客户端连接端口  

使用单机模式时用户需要注意：这种配置方式下没有 ZooKeeper 副本，所以如果 ZooKeeper 服务器出现故障， ZooKeeper 服务将会停止。

#### 运行脚本启动

``` bash
$ cd zookeeper-3.4.10
$ ./bin/zkServer.sh start
```
