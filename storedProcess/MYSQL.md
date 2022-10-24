---
layout: default
title: StoredProcess--MYSQL
nav_order: 1
parent： StoredProcess
---

# mysql 存储过程   
## 存储过程相当于将sql进行封装与重用，只需要编译一次，所以运行速度比直接执行sql语句要快  
### 优点：  
存储过程可封装，可以隐藏复杂的商业逻辑  
存储过程可以返回值，也可以传参数  
存储过程无法使用select指令来运行，因为它是子程序，与查看表，数据表或用户定义函数不同  
存储过程可以用再数据校验，强制实行商业逻辑等  
### 缺点：  
受限于各种数据库系统  
当切换到其他厂商的数据库时，需要重写原有的存储过程  
## 一、创建存储过程  
````mysql
CREATE
    [DEFINER = { user | CURRENT_USER }]
　PROCEDURE sp_name ([proc_parameter[,...]])
    [characteristic ...] routine_body
 
proc_parameter:
    [ IN | OUT | INOUT ] param_name type
 
characteristic:
    COMMENT 'string'
  | LANGUAGE SQL
  | [NOT] DETERMINISTIC
  | { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
  | SQL SECURITY { DEFINER | INVOKER }
 
routine_body:
　　Valid SQL routine statement
 
[begin_label:] BEGIN
　　[statement_list]
　　　　……
END [end_label]
````

### mysql存储过程中的关键语法
- 局部变量作用范围在begin--end之间。用户变量作用范围在整个会话间，与客户端绑定的，只对当前用户使用的客户端生效，形式如：@变量名。
- 1.声明语句结束符
  DELIMITER $$
  或
  DELIMITER //
  
- 声明存储过程
````
  CREATE PROCEDURE demo_in_parameter(IN p_in int)
````
- 存储过程的开始和结束符号
````
  BEGIN ....END
````
- 变量赋值
````
  SET @p_in=1
````
- 变量定义
````
  DECLARE l_int int unsigned default 4000000; 
````
- 创建mysql存储过程、存储函数
````
create procedure 存储过程名(参数)
````
- 存储过程体
````
  create function 存储函数名(参数)
````
- 调用存储过程
````
call sp_name([传参]);
````

### 注意：
默认情况下，存储过程和默认数据库相关联，如果想指定存储过程创建在某个特定的数据库下，  
那么在过程名前面加数据库名做前缀。 在定义过程时，使用 DELIMITER $$ 命令将语句的结束符号从分号 ;  
临时改为两个 $$，使得过程体中使用的分号被直接传递到服务器，而不会被客户端（如mysql）解释。  

### 存储过程体
- 存储过程体包含了在过程调用时必须执行的语句，例如：dml、ddl语句，if-then-else和while-do语句、声明变量的declare语句等  
- 过程体格式：以begin开始，以end结束(可嵌套)  
- 每个嵌套块及其中的每条语句，必须以分号结束，表示过程体结束的begin-end块(又叫做复合语句compound statement)，则不需要分号。  


## 二、存储过程的参数

### 参数有IN 、OUT、INOUT三种类型
- IN 输入参数：表示调用者向过程传入值
- OUT 输出参数：表示过程向调用者传出值
- INOUT 输入输出值：既表示调用者向过程传入值，又表示过程向调用者传出值。（一般不用）

## 三、变量
### 1.变量定义
局部变量声明一定要放在存储过程体的开始
````
DECLARE variable_name [,variable_name...] datatype [DEFAULT value];
````
datatype为mysql的数据类型，如：int，float，date，varchar(length)
例如：DECLARE l_int int unsigned default 4000000;
### 2.变量赋值
````
set 变量名 = 表达式值[,variable_name = expression ...]
````
### 3.用户变量
在mysql客户端使用用户变量
-注意：用户变量名一般以@开头，滥用用户变量会导致程序难以理解及管理

## 四、注释
### mysql存储过程的查询
````
select name from mysql.proc where db='数据库名';

或者

select routine_name from information_schema.routines where routine_schema='数据库名';

或者

show procedure status where db='数据库名';
````
- 查看存储过程的详细
````
SHOW CREATE PROCEDURE 数据库.存储过程名;
````
- 存储过程的删除
````
drop procedure
````
- 存储过程的修改
````
alter procedure
````

- 存储过程的控制语句    
(1).变量作用域    
  内部的变量在其作用域范围内享有更高的优先权，当执行到 end。变量时，内部变量消失，此时已经在其作用域外，变量不再可见了，应为在存储过程外再也不能找到这个申明的变量，但是你可以通过 out 参数或者将其值指派给会话变量来保存其值。   
(2).条件语句     
if-then-else 语句  
````
  if-then-else  
  end if
````
case语句
````
  case 
  when then 
  else 
  end case
````
(3).循环语句
1. while ···· end while
````
while 条件 do 
   循环体
   end while
````
2.repeat···· end repeat
它在执行操作后检查结果，而 while 则是执行前进行检查。
````
repeat
    --循环体
until 循环条件  
end repeat;
````
3.loop ·····end loop
````
mysql > DELIMITER //  
mysql > CREATE PROCEDURE proc6 ()  
     -> begin 
     -> declare v int;  
     -> set v=0;  
     -> LOOP_LABLE:loop  
     -> insert into t values(v);  
     -> set v=v+1;  
     -> if v >=5 then 
     -> leave LOOP_LABLE;  
     -> end if;  
     -> end loop;  
     -> end;  
     -> //  
mysql > DELIMITER ;
````
loop 循环不需要初始条件，这点和 while 循环相似，同时和 repeat 循环一样不需要结束条件, leave 语句的意义是离开循环。

4. LABLES 标号：
标号可以用在 begin repeat while 或者 loop 语句前，语句标号只能在合法的语句前面使用。可以跳出循环，使运行指令达到复合语句的最后一步。

(4).ITERATE迭代：ITERATE 通过引用复合语句的标号,来从新开始复合语句


## 出现中文乱码情况  
原存储过程sql：
````mysql
create procedure test()
begin
declare num,cla,author,times INT;
declare titles VARCHAR(60);
declare subtitles,outlines,contents VARCHAR(100);
set num=1;
while num <= 200 do
set cla=FLOOR(RAND()*19);
set author=FLOOR(RAND()*899);
set times=1578376543+num;
set titles=CONCAT('这是第',num,'篇文章的标题');
set subtitles=CONCAT('这是第',num,'篇文章的副标题，是标题的附属说明');
set outlines=CONCAT('这是第',num,'篇文章的概述内容，是文章完整内容的概括描述，用于快速阅览。');
set contents=CONCAT('这是第',num,'篇文章的详细内容，也就是详情页要展示的主要内容，是多行文本，一般都会比较长。');
insert into article(class_id,author_id,up_time,title,subtitle,outline,content)VALUES(cla,author,times,titles,subtitles,outlines,contents);
set num=num+1;
end while;
end;
````
### 问题原因：插入文本之前没有指定编码，MySQL工具就使用了电脑系统当前的默认编码，就造成了编码不一致的问题，所以就出现了乱码问题。  
### 解决办法：在 varcher() 后面指定编码格式：CHARACTER SET utf8 -->例如第四行：declare titles VARCHAR(60)-->declare titles VARCHAR(60) CHARACTER SET utf8。






--参考 [资料](https://www.runoob.com/w3cnote/mysql-stored-procedure.html)