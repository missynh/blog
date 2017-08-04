---
title: VPS上安装nginx  
date: 2017-04-10  
tags: nginx
---
### 安装编译环境gcc,gcc-c++

``` bash
$ yum -y install gcc automake autoconf libtool make
$ yum install gcc gcc-c++
```

<!--more-->

### 安装pcre
源码目录/usr/local/src

```bash
$ cd /usr/local/src
$ mkdir obj
$ cd obj
$ wget https://ftp.pcre.org/pub/pcre/pcre-8.40.tar.gz
$ tar -zxvf pcre-8.40.tar.gz
$ cd pcre-8.40
$ ./configure
$ make & make install
```

### 安装zlib

``` bash
$ cd /usr/local/src/obj
$ wget http://www.zlib.net/zlib-1.2.11.tar.gz
$ tar -zxvf zlib-1.2.11.tar.gz
$ cd  zlib-1.2.11
$ ./configure
$ make & make install
```

### 安装openssl

``` bash
$ cd /usr/local/src/obj
$ wget https://www.openssl.org/source/openssl-1.1.0e.tar.gz
$ tar -zxvf openssl-1.1.0e.tar.gz
$ cd  openssl-1.1.0e
$ ./config
$ make & make install
```

### 安装nginx

``` bash
$ cd /usr/local/src
$ wget  http://nginx.org/download/nginx-1.11.10.tar.gz
$ tar -zxvf nginx-1.11.10.tar.gz
$ cd nginx-1.11.10
$ ./configure --prefix=/usr/local/nginx --with-http_ssl_module --with-http_v2_module --with-pcre=../obj/pcre-8.40 --with-zlib=../obj/zlib-1.2.11 --with-openssl=../obj/openssl-1.1.0e
$ make & make install
```

### 启动nginx

``` bash
$ cd /usr/local/nginx/sbin
$ ./nginx
```
启动时可能会出现80端口占用错误，杀死占用端口进程，便能正常启动

``` bash
$ lsof -i:80
$ kill -9 对应进程号
$ ./nginx
```

### 开机自启动

```bash
$ vim /etc/rc.local
> /usr/local/nginx/sbin/nginx
```