---
title: linux上安装redis详细步骤
date: 2017-07-26  
tags: redis
---
#### 下载源码并解压编译

redis官网地址：http://www.redis.io/

```bash
$ wget http://download.redis.io/releases/redis-4.0.1.tar.gz
$ tar -zxvf redis-4.0.1.tar.gz
$ cd redis-4.0.1
$ make
```

<!--more-->

#### 复制可执行文件至/usr/redis

编译完成后，再redis-4.0.1/src下有四个可执行文件：redis-server，redis-benchmark，redis-cli，redis.conf.将其复制进/usr/redis下

```bash
$ cp redis-server redis-benchmark redis-cli redis.conf /usr/redis
```

#### 启动redis

```bash
$ cd /usr/redis
$ ./redis-server
```

#### 将其设置为后台运行

用vim打开配置文件redis.conf，找到daemonize=no，将no修改为yes，保存并退出。

```bash
$ vim redis.conf
```
<img src="http://oo8ieb5e5.bkt.clouddn.com/image/jpg/redis-daemonize.png" />

运行下面命令，使redis在后台运行
```bash
$ ./redis-server redis.conf
```

#### 打开redis客户端
待服务启动后，再执行“./redis-cli”，打开redis客户端，测试redis是否可以正常工作了。
```bash
$ ./redis-cli
$ set test "成功啦"
$ get test
```

#### 限制redis连接
方案1：配置文件中设置允许登录的客户端IP地址：
   如：bind 127.0.0.1;
方案2：redis客户端设置密码
    如：config set requirepass "password"

友情提示：基于我的VPS通过redis被入侵作为别人的挖矿工具的惨痛教训，提示您千万不要将redis的端口号暴露在公网中。