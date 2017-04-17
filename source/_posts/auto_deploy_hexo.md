---
title: 利用lua脚本实现hexo自动部署
date: 2017-04-11  
tags: [hexo,lua,nginx,webhooks,github]
---
本篇使用的是github的webhooks+nginx+lua脚本实现hexo提交时自动部署
#### github配置webhooks
首先在github中初始化一个blog项目，之后配置一个webhooks，如下图
  
<!--more-->

<img src="http://oo8ieb5e5.bkt.clouddn.com/image/vps/github_webhooks.png" />   <br><br>
这样当你向这个项目push时，就会发送一个post请求，你的nginx这时便可收到这个请求，那么你就可以为所欲为了，嘿嘿.....

#### nginx配置
在这之前你需要装好lua或者Luajit以及nginx编译了lua-nginx-module模块  
<img src="http://oo8ieb5e5.bkt.clouddn.com/image/vps/nginx_webhooks_conf.png" />

现在开始编写blog_hook.lua的lua脚本
这里也就是执行以下www/blog.update.sh脚本,成功后返回"OK"
``` bash
$ os.execute("sh /www/blog_update.sh");
$ ngx.say("OK")
```

#### shell脚本
``` bash
#!/bin/bash
export PATH=$PATH:/usr/local/nodejs/bin
export LANG="en_US.UTF-8"
unset GIT_DIR 
NowPath=`pwd`
WebSite="/www"
BlogPath="/www/blog"
if [ ! -d "$BlogPath" ]; then
  cd $WebSite
  git clone git@github.com:missynh/blog.git
  cd $BlogPath
else
  cd $BlogPath
  git fetch --all
  git reset --hard origin/master
fi
npm install hexo-cli -g
npm install
npm i hexo-generator-json-content --save
hexo clean
hexo g
```
执行时遇到没有权限的错误,解决办法: 修改nginx.conf中的user为root


