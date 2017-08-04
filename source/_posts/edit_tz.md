---
title: linux系统修改时区
date: 2017-08-04
tags: [tz]
---

由于我自己的VPS是买的国外的，所以默认的时区的UTC，今天VPS被人入侵，使用last who等命令显示出来的日期看着比较费劲，随即修改时区，记性不好，还是记录一下吧.....

#### 修改配置文件
你可以使用tzselect命令选择你想要修改的时区
```bash
$ tzselect
You can make this change permanent for yourself by appending the line
	TZ='Asia/Shanghai'; export TZ
to the file '.profile' in your home directory; then log out and log in again.

Here is that TZ value again, this time on standard output so that you
can use the /usr/bin/tzselect command in shell scripts:
Asia/Shanghai
```

然后根据提示将TZ='Asia/Shanghai'; export到你的profile配置文件中。并关机重启使之生效 

```bash
$ vim /etc/profile
> export TZ='Asia/Shanghai'
$ shutdown -r now
$ date
```