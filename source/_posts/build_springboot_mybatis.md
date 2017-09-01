---
title: 搭建springboot+mybatis轻量级框架 
date: 2017-07-28  
tags: [springboot, mybatis]
---
年初的时候使用springboot+mybatis搭建过一个小框架，一直没有时间整理总结，好在手上项目已到尾声，可以抽出点时间整理这段时间所学的知识，此篇文章想要记录一下我在四院这个项目中框架的构建。
先附上demo项目地址：https://github.com/missynh/springboot-demo.git

<!--more-->

#### application.properties
```bash
server.port=8080
server.context-path=/

logging.level.cn.cesi.evaluation.mapper=debug
logging.config=classpath:logback.xml

spring.datasource.name=evaluation
spring.datasource.url=jdbc:mysql://172.0.0.1/test
spring.datasource.username=test
spring.datasource.password=test
# 使用druid数据源
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.filters=stat
spring.datasource.maxActive=20
spring.datasource.initialSize=1
spring.datasource.maxWait=60000
spring.datasource.minIdle=1
spring.datasource.timeBetweenEvictionRunsMillis=60000
spring.datasource.minEvictableIdleTimeMillis=30000
spring.datasource.validationQuery=select 'x'
spring.datasource.testWhileIdle=true
spring.datasource.testOnBorrow=false
spring.datasource.testOnReturn=false
spring.datasource.poolPreparedStatements=true
spring.datasource.maxOpenPreparedStatements=20

spring.messages.encoding=UTF-8

spring.mvc.view.prefix=/templates/
spring.mvc.view.suffix=.ftl

spring.freemarker.cache=false
spring.freemarker.request-context-attribute=request

mybatis.typeAliasesPackage=cn.cesi.evaluation.model
mybatis.mapper-locations=classpath:mapper/*.xml
mybatis.config-location=classpath:mybatis-config.xml

mapper.mappers[0]=cn.cesi.evaluation.utils.MyMapper
mapper.not-empty=false
mapper.identity=MYSQL

mybatis.pagehelper.helperDialect=mysql 
mybatis.pagehelper.reasonable=true
mybatis.pagehelper.supportMethodsArguments=true
mybatis.pagehelper.params= count=countSql


# REDIS (RedisProperties)
# Redis数据库索引（默认为0）
spring.redis.database=0
spring.redis.host=172.0.0.1
spring.redis.port=19000
# Redis服务器连接密码（默认为空）
spring.redis.password=
# 连接池最大连接数（使用负值表示没有限制）
spring.redis.pool.max-active=8
# 连接池最大阻塞等待时间（使用负值表示没有限制）
spring.redis.pool.max-wait=-1
# 连接池中的最大空闲连接
spring.redis.pool.max-idle=8
# 连接池中的最小空闲连接
spring.redis.pool.min-idle=0
# 连接超时时间（毫秒）
spring.redis.timeout=0

# mail
spring.mail.host=imap.exmail.qq.com
spring.mail.username=123@456.com
spring.mail.password=xxxxx
spring.mail.properties.mail.smtp.auth=true

spring.http.multipart.maxFileSize=1000Mb
spring.http.multipart.maxRequestSize=10000Mb
```

未完待续....