---
title: mysql循环语句
date: 2017-08-07  
tags: [mysql, procedure]
---
我们通常在数据库中要创建很多很多条测试数据，一味的复制粘贴总觉得有些麻烦。这时我们可以使用mysql的循环语句

### while循环
```bash
delimiter $$
drop procedure  if exists wk;
CREATE PROCEDURE wk ()
BEGIN

DECLARE i INT;

SET i = 1;

WHILE i < 90 DO
	sql 语句
SET i = i + 1;

END
WHILE;

end $$
```

<!-- more -->

mysql默认的delimiter是分号 他告诉mysql解释器，该段命令是否已经结束了，mysql是否可以执行了。通常情况下语句中可能含有多个分号,如果不想在遇到第一个分号后就立即执行语句，
那么你就可以重新定义结束符delimiter了。

创建存储过程语法：
```bash
CREATE PROCEDURE 存储过程名称([in | out | inout] 参数)

BEGIN

myqsl语句

END
```

调用存储过程
```bash
call 存储过程名称()
```

### repeat循环
```bash
delimiter //
drop procedure if exists looppc;
create procedure looppc()
begin 
declare i int;
set i = 1;

repeat 
    insert into user_profile_company (uid) values (i+1);
    set i = i + 1;
until i >= 20

end repeat;

end //
```

### loop循环
```bash
delimiter $$
drop procedure if exists lopp;
create procedure lopp()
begin 
declare i int ;
set i = 1;

lp1 : LOOP　　　　　　　　　　　　　　//  lp1 为循环体名称   LOOP 为关键字
    insert into user_profile (uid) values (i);
    set i = i+1;
    if i > 30 then
leave lp1;　　　　　　　　　　　　　　//  离开循环体
    end if;
end LOOP;　　　　　　　　　　　　　　//  结束循环
end $$
```