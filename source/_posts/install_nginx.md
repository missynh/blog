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
方法一.将启动命令添加至/etc/rc.local中

```bash
$ vim /etc/rc.local
> /usr/local/nginx/sbin/nginx
```

方法二.使用脚本启动
首先，在linux系统的/etc/init.d目录下创建nginx文件：
```bash
$ vim /etc/init.d/nginx
```

在脚本中添加以下命令:
```bash
#!/bin/sh
#
# nginx - this script starts and stops the nginx daemon
#
# chkconfig:   - 85 15
# description:  NGINX is an HTTP(S) server, HTTP(S) reverse \
#               proxy and IMAP/POP3 proxy server
# processname: nginx
# config:      /etc/nginx/nginx.conf
# config:      /etc/sysconfig/nginx
# pidfile:     /var/run/nginx.pid
# Source function library.
. /etc/rc.d/init.d/functions
# Source networking configuration.
. /etc/sysconfig/network
# Check that networking is up.
[ "$NETWORKING" = "no" ] && exit 0
nginx="/usr/sbin/nginx"
prog=$(basename $nginx)
NGINX_CONF_FILE="/etc/nginx/nginx.conf"
[ -f /etc/sysconfig/nginx ] && . /etc/sysconfig/nginx
lockfile=/var/lock/subsys/nginx
make_dirs() {
   # make required directories
   user=`$nginx -V 2>&1 | grep "configure arguments:" | sed 's/[^*]*--user=\([^ ]*\).*/\1/g' -`
   if [ -z "`grep $user /etc/passwd`" ]; then
       useradd -M -s /bin/nologin $user
   fi
   options=`$nginx -V 2>&1 | grep 'configure arguments:'`
   for opt in $options; do
       if [ `echo $opt | grep '.*-temp-path'` ]; then
           value=`echo $opt | cut -d "=" -f 2`
           if [ ! -d "$value" ]; then
               # echo "creating" $value
               mkdir -p $value && chown -R $user $value
           fi
       fi
   done
}
start() {
    [ -x $nginx ] || exit 5
    [ -f $NGINX_CONF_FILE ] || exit 6
    make_dirs
    echo -n $"Starting $prog: "
    daemon $nginx -c $NGINX_CONF_FILE
    retval=$?
    echo
    [ $retval -eq 0 ] && touch $lockfile
    return $retval
}
stop() {
    echo -n $"Stopping $prog: "
    killproc $prog -QUIT
    retval=$?
    echo
    [ $retval -eq 0 ] && rm -f $lockfile
    return $retval
}
restart() {
    configtest || return $?
    stop
    sleep 1
    start
}
reload() {
    configtest || return $?
    echo -n $"Reloading $prog: "
    killproc $nginx -HUP
    RETVAL=$?
    echo
}
force_reload() {
    restart
}
configtest() {
  $nginx -t -c $NGINX_CONF_FILE
}
rh_status() {
    status $prog
}
rh_status_q() {
    rh_status >/dev/null 2>&1
}
case "$1" in
    start)
        rh_status_q && exit 0
        $1
        ;;
    stop)
        rh_status_q || exit 0
        $1
        ;;
    restart|configtest)
        $1
        ;;
    reload)
        rh_status_q || exit 7
        $1
        ;;
    force-reload)
        force_reload
        ;;
    status)
        rh_status
        ;;
    condrestart|try-restart)
        rh_status_q || exit 0
            ;;
    *)
        echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
        exit 2
esac
```
脚本地址可参考nginx官方文档：https://www.nginx.com/resources/wiki/start/topics/examples/redhatnginxinit/
注意：如果你是自定义编译安装的nginx，你需要根据自己的安装路径修改下面两项配置：
1.nginx="/usr/sbin/nginx" #修改成nginx执行程序的路径
2.NGINX_CONF_FILE="/etc/nginx/nginx.conf" #修改成配置文件的路径

保存脚本文件后设置文件的执行权限

```bash
$ chmod +x /etc/init.d/nginx
```

然后，你就可以通过该脚本对nginx服务进行管理了

```bash
$ /etc/init.d/nginx start
$ /etc/init.d/nginx stop
```

使用chkconfig进行管理
上面的步骤实现了用脚本管理nginx服务的功能，但是还是不太方便，每次开机重启后都需要执行一下上述命令，这时候chkconfig就派上用场了。
先将nginx服务加入chkconfig管理列表

```bash
$ chkconfig --add /etc/init.d/nginx
$ chkconfig --list
```

加完这个之后就可以使用service对nginx进行启动，重启等操作了
```bash
$ service nginx start
$ service nginx stop
```

设置终端模式开机自启动
```bash
$ chkconfig nginx on
```
