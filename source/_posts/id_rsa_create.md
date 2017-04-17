---
title: linux生成SSH Keys  
date: 2017-04-11  
tags: [linux,SSH Keys]
---
#### 何为SSH Key
SSH key提供了一种与GitHub通信的方式，通过这种方式，能够在不输入密码的情况下，将GitHub作为自己的remote端服务器，进行版本控制

#### 步骤
1.检查SSH keys是否存在
2.生成新的ssh key
3.将ssh key添加到GitHub中

#### 检查SSH Keys是否存在
``` bash
ls -al ~/.ssh
```

<!--more-->

#### 生成新的ssh key
``` bash
$ ssh-keygen -t rsa -C "1239391682@qq.com"
> Generating public/private rsa key pair.
> Enter file in which to save the key (/root/.ssh/id_rsa): 
```

#### 将新生成的key添加到ssh-agent中
``` bash
$ eval "$(ssh-agent -s)"
> Agent pid 59566
$ ssh-add ~/.ssh/id_rsa
```

#### 将ssh key添加到GitHub中
将id_rsa_pub中的字符串添加至github中就大功告成了