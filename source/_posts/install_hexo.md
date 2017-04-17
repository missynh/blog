---
title: 安装hexo  
date: 2017-04-10  
tags: hexo
---
### 一、VPS安装nodejs
``` bash
$ wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-x64.tar.xz
$ tar -xf node-v6.10.2-linux-x64.tar.xz
$ mv node-v6.10.2-linux-x64 nodejs
$ vim /etc/profile
> export PATH=$PATH:/usr/local/nodejs/bin
```
注：配置nodejs环境变量，将当前配置生效，/etc/profile-配置全局系统变量，~/root/.bashrc-配置当前用户的环境变量，仅对当前用户生效

<!--more-->

``` bash
$ source /etc/profile
```

### 二、windows下配置hexo和nodejs，以便在windows上调试

#### 1.下载nodejs安装包，一键安装
附官方下载地址：https://nodejs.org/en/download

#### 2.安装hexo

``` bash
$ npm install -g hexo-cli
```

#### 3.初始化hexo

``` bash
$ hexo init
```
安装hexo过程中遇到"hexo不是内部或外部命令"错误，错误原因:环境变量没有配置，  
解决办法：在环境变量中加上 C:\Program Files\nodejs\node_global\node_modules\hexo\node_modules\.bin。<br><br>
到此博客的基本框架就构建完成了，如果不想修改主题的话就可以用当前的默认主题。

#### 4.将markdown文档生成.html静态文件
注：生成的站点文件默认放在public文件夹中
``` bash
$ hexo g
```

#### 5.启动hexo服务
``` bash
$ hexo server
```

#### 6.修改默认主题
你可以在gitlab上下载你想要的主题。将下载好的主题放在themes文件夹下，修改_config.yml中的theme参数就完成了。

### 三、VPS上部署
首先你的vps上需要安装好nginx，如果没安装的可以参考上一篇[VPS上安装nginx](http://yangnenghui.cn/2017/04/10/install_nginx/)
修改nginx配置root指向hexo生成的静态文件目录
``` bash
$ vim /usr/local/nginx/conf/nginx.conf
> server {
>  listen 80;                                       #监听80端口
>  server_name yangnenghui.cn www.yangnenghui.cn;   #域名
 
> location / {
>   root /www/blog/public;                          #博客目录下的public目录（静态文件）
>   index index.html index.htm;                     #首页
>   }
 
> error_page 500 502 503 504 /50x.html;             #出现500 502 503 504错误，跳转到对应的错误页面
>   location = /50x.html {
>   root html;
>   }
> }
```