---
title: 连接VPS  
date: 2017-04-10  
tags: VPS
---
### 修改连接方式
vps默认连接方式为密码连接，如果想通过公钥连接，需要修改服务器中的/etc/ssh/sshd_config配置

``` bash
$ vim sshd_config
> PubkeyAuthentication yes                        #是否允许公钥登录
> AuthorizedKeysFile   .ssh/authorized_keys       #公钥文件目录
```

### 添加公钥  
在root下新建.ssh文件夹，将自己电脑的公钥复制进authorized_keys文件中

### 重启ssh服务

``` bash
$ systemctl reload sshd
```