---
title: 苏宁面试题
date: 2018-03-19
tags: [苏宁, 面试题]
---
1.开场自我介绍，简述最近做的一个项目，问了一些权限系统相关的流程，画一下表设计
2.解释一下dubbo的机制，dubbo和restful的区别
3.数据库的索引类型，主键索引和唯一索引的区别
<!-- more -->
4.手写了三个sql
(1)有一个表有a,b,c是是哪个字段，取出第十页的数据
```bash
SELECT
tb1.a,
tb1.b,
tb1.c
FROM
tb1 tb1
limit 90,100 
```
(2)有一个tb1表，tb2表，tb1有a,b,c列，tb2有a,b,d三个字段；请按tb1的a，b字段进行分组，并按tb1
的a字段升序排序，tb2的b字段降序排列，最后取出tb1的a的最大值
```bash
SELECT
max(tb1.a)
FROM
tb1 tb1, 
tb2 tb2
GROUP BY
tb1.a,tb2.b
ORDER BY 
tb1.a asc,tb2.b desc
```
(3)有一个tb1,表，tb2表，tb1有a,b,c列，tb2有a,b,d三个字段；请找出tb1中存在tb2中不存在的a。如果在tb1,tb2的a字段上加上索引，你可以写出不全表扫描的sql吗
``` bash
SELECT
tb1.a
FROM
tb1 tb1
WHERE 
(
    SELECT
    count(*)
    FROM
    tb2 tb2
    WHERE
    tb1.a=tb2.a
) = 0
```
5.redis可以存哪些类型的值，用redis做过哪些东西？redis锁是怎么实现的？
6.给我说一下jpa
7.zookeeper了解吗，简单说一下
8.nginx是用来干什么的？里面有哪些配置项
9.springMVC中DispatcherServlet是配置在哪个文件中的？HandlerMapper是用来干什么的？filter，listener，servlet三个的加载顺序是什么？

前面花了很长时间聊了两个项目的业务，穿插的问题有点记不清了，记住的问题就这么多....

笔记：
1.DispatcherServlet的作用
  DispatcherServlet是前置控制器，配置在web.xml中。拦截匹配的请求，servlet拦截匹配规则要自己定义，把拦截下来的请求，依据相应的规则分发到目标Controller来处理。