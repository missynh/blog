---
title: VPS上安装luajit
tags: lua
---
#### 安装luajit
``` bash
wget http://luajit.org/download/LuaJIT-2.0.4.tar.gz
tar -zxvf LuaJIT-2.0.4.tar.gz
rm -rf LuaJIT-2.0.4.tar.gz
cd LuaJIT-2.0.4
make & make install

cat /etc/ld.so.conf
echo "/usr/local/lib" >> /etc/ld.so.conf
ldconfig
```

#### 添加lua-nginx-module

``` bash
wget  http://nginx.org/download/nginx-1.11.13.tar.gz
tar -zxvf nginx-1.11.13.tar.gz
cd nginx-1.11.13
./configure --prefix=/usr/local/nginx --with-http_ssl_module --with-http_v2_module --with-pcre=../obj/pcre-8.40 --with-zlib=../obj/zlib-1.2.11 --with-openssl=../obj/openssl-1.0.2k --add-module=/usr/local/src/nginx-module/lua-nginx-module
make & make install
```
