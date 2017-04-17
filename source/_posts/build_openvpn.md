---
title: 搭建openvpn  
date: 2017-04-13  
tags: VPN
---
### 安装组件

``` bash
$ yum install epel-release
$ yum install ocserv
```
<!--more-->
### 生成证书  
1.生成ca证书

``` bash
$ cd /etc/ocserv
$ certtool --generate-privkey --outfile ca-key.pem
$ vim ca.tmpl
```
复制粘贴以下语句

cn = "VPN CA"  
organization = "Big Corp"  
serial = 1  
expiration_days = 7777  
ca  
signing_key  
cert_signing_key  
crl_signing_key

``` bash
$ certtool --generate-self-signed --load-privkey ca-key.pem --template ca.tmpl --outfile ca-cert.pem
```

2.生成server证书

``` bash
$ certtool --generate-privkey --outfile server-key.pem
$ vim server.tmpl
```
复制粘贴以下语句

cn = "yangnenghui.cn"  
organization = "MyCompany"  
expiration_days = 7777  
signing_key  
encryption_key  
tls_www_server

``` bash
$ certtool --generate-certificate --load-privkey server-key.pem --load-ca-certificate ca-cert.pem --load-ca-privkey ca-key.pem --template server.tmpl --outfile server-cert.pem
```

期间按提示设置证书密码，也可以不设置密码

3.客户端证书 user-cert.pem 使用OpenSSL转换成 .p12格式

``` bash
$ openssl pkcs12 -export -inkey user-key.pem -in user-cert.pem -name "client" -certfile ca-cert.pem -caname "VPN CA" -out user-cert.p12
```

4.复制到对应位置

``` bash
$ cp ca-cert.pem /etc/ssl/certs/
$ cp server-cert.pem /etc/ssl/certs/
$ cp server-key.pem /etc/ssl/private/
```

#### 配置文件

``` bash
$ getent group ocserv &>/dev/null || groupadd -r ocserv
$ useradd -r -g ocserv -s /sbin/nologin -c ocserv
$ vim  /etc/ocserv/ocserv.conf
```
```
# 使用证书验证
auth = "certificate"
# 最大连接数
max-clients = 16
# 单IP最大连接数，默认是2
max-same-clients = 10
# 端口
tcp-port = 1443
udp-port = 1443
keepalive = 32400
# Dead Peer检测频率
dpd = 240
# 移动设备用的, 只要发送了 X-AnyConnect-Identifier-DeviceType 头, ocs 就会发送这个 dpd
mobile-dpd = 1800
try-mtu-discovery = true
# 证书位置
server-cert = /etc/ssl/certs/server-cert.pem
server-key = /etc/ssl/private/server-key.pem
ca-cert = /etc/ssl/certs/ca-cert.pem
tls-priorities = "NORMAL:%SERVER_PRECEDENCE:%COMPAT"
auth-timeout = 40
mobile-idle-timeout
cookie-timeout = 86400000
rekey-time = 86400000
rekey-method = ssl
use-utmp = true
use-occtl = true
pid-file = /var/run/ocserv.pid
occtl-socket-file = /var/run/ocserv-socket
socket-file = ocserv.sock
# 运行用户组
run-as-user = ocserv
run-as-group = ocserv
net-priority = 5
cgroup = "cpuset,cpu:test"
# VPN的设备名称, 之后做策略的时可能用到
device = vpns
default-domain = yangnenghui.cn
# 分配给VPN客户端的IP段
ipv4-network = 10.10.0.0
ipv4-netmask = 255.255.255.0
dns = 8.8.8.8
dns = 8.8.4.4
ping-leases = false
output-buffer = 10
#route = ip网段/子网掩码
# 全局路由
#route = 0.0.0.0/128.0.0.0
#route = 128.0.0.0/128.0.0.0

# anyconnect 连接需要设置 true
cisco-client-compat =true
#custom-header = "X-DTLS-MTU: 1200"
#custom-header = "X-CSTP-MTU: 1200"
cert-user-oid = 2.5.4.3
no-route = 
```

#### 防火墙端口开放
创建防火墙 service 规则文件

``` bash
$ vim /etc/firewalld/services/ocserv.xml
```
写入
``` bash
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>ocserv</short>
  <description>Cisco AnyConnect</description>
  <port protocol="tcp" port="1433"/>
  <port protocol="udp" port="1433"/>
</service>
```

添加防火墙规则，重加载

``` bash
$ firewall-cmd --permanent --add-service=ocserv
$ firewall-cmd --permanent --add-masquerade
$ firewall-cmd --reload
```

#### 调试运行
``` bash
$ ocserv -c /etc/ocserv/ocserv.conf -f -d 1
```
启动服务
``` bash
$ /etc/init.d/ocserv restart
```
或者将其加入开机自启动项并启动
``` bash
$ systemctl enable ocserv
$ systemctl start ocserv
$ systemctl status ocserv
```

#### VPN端口转发
``` bash
$ vim /etc/sysctl.conf
```
写入
```
net.netfilter.nf_conntrack_max = 64000
net.ipv4.ip_forward = 1
```

安装net工具
```
$ yum install net-tools -y
$ ifconfig
```
<img src="http://oo8ieb5e5.bkt.clouddn.com/image/vps/ifconfig.png" /> <br><br>

一次性配置
``` bash
$ iptables -A FORWARD -i vpns0 -o venet0:0 -j ACCEPT
$ iptables -A FORWARD -i venet0:0 -o vpns0 -m state --state ESTABLISHED,RELATED -j ACCEPT
$ iptables -t nat -A POSTROUTING -j MASQUERADE
$ iptables -A FORWARD -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --clamp-mss-to-pmtu
$ sysctl -p /etc/sysctl.conf
```

#### 连接
下载anyconnet客户端，配置服务器端口等  
安装上面生成的user-cert.p12证书  
连接，完成........

