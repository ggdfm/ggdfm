---
layout: default
title: StoredProcess--ORACLE
nav_order: 2
parent: StoredProcess
---

## ORACLE
{: .no_toc }

## oracle 处理乱码 --全表查找乱码，并将乱码所处表与列标记在临时表中。
````oracle
create or replace procedure check_zh_cn is
    v_table_name  varchar2(50) ;
    v_table_column  varchar2(50) ;
    v_sql  varchar2(1000) ;
    v_count  int(10) := 0;
    v_index  int(10) := 0;
    -- 查询所有的表
    cursor v_table_names is
        SELECT * FROM USER_TABLES U;
begin
    for v_table_data in v_table_names loop
      begin
        v_table_name := v_table_data.TABLE_NAME ;
        -- 否则查询出表的列数据
        for v_table_column_data in (select * from user_tab_columns where Table_Name=v_table_name ORDER BY COLUMN_ID ASC ) loop

            begin
            v_table_column := v_table_column_data.COLUMN_NAME ;
            if  instr(v_table_column_data.DATA_TYPE, 'VARCHAR') > 0 then
                -- 判断乱码
                v_sql := 'select count(1) from '|| v_table_name || ' where asciistr('|| v_table_column ||') like ''%??%'' or asciistr('|| v_table_column ||') like ''%\FFFD%'' ';
                execute immediate v_sql into v_count;
                if v_count >0 then
                   v_index := v_index+1;
                   DBMS_OUTPUT.PUT_LINE(v_index ||' table.column is ' || v_table_name || '.' || v_table_column);
                end if;
            end if;
            end;
        end loop;
      end;
    end loop ;
end;
````
-[内容部分转载于此链接](https://blog.csdn.net/weixin_44740485/article/details/120995693)

### 存储过程语法说明
#### 1.存储过程的基本结构
- 带参存储过程语法(无参存储过程语法与有参差别在于没有存储过程名字后的括号：CREATE OR REPLACE PROCEDURE 存储过程名字 IS ...)

````sql
CREATE OR REPLACE PROCEDURE 存储过程名字	(
	    --输入参数 IN NUMBER,
	    --输出参数 OUT NUMBER --可以有多个输入参数和输出参数
	) IS
	    --这个区间可以用来定义变量和创建游标
	BEGIN
	    --执行游标或者执行语句
	     commit; --对上面的内容进行提交
	exception//存储过程异常
	   when others then
	   	   --异常处理方法，可以是打印错误，然后进行回滚等操作，下面操作一样，看自己情况决定
	   	   rollback;
	   	   dbms_output.put_line(sqlcode);
	   	   dbms_output.put_line(substr(sqlerrm, 1, 512));
	END 存储过程名字;
-- 	注意：其中参数IN表示输入参数，是参数的默认模式。
-- 				OUT表示返回值参数，类型可以使用任意Oracle中的合法类型。
-- 				OUT模式定义的参数只能在过程体内部赋值，表示该参数可以将某个值传递回调用他的过程
-- 				IN OUT表示该参数可以向该过程中传递值，也可以将某个值传出去。
````    
#### 2.变量的声明和赋值
- 变量的声明

````
    a、按照数据类型进行声明
	格式：变量名 变量的数据类型;
	样例：v_name varchar2(500);
	b、根据表字段进行声明
	格式：变量名 表名.字段名%TYPE
	样例：v_name userinfo.name%TYPE;
	注意：变量的数据类型和表名的字段数据类型一致
	c、根据表的行记录进行声明
	格式：变量名 表名/游标名%ROWTYPE
	样例：v_user_row userinfo%ROWTYPE;
	注意：变量的数据类型和表的行数据类型一致
````    
- 变量的赋值  符号为 <font color='red'>:=</font>

````
    a、直接赋值，需要使用 := 进行赋值
            使用的条件：只能在变量的声明a和b才能使用直接赋值
            先声明在赋值（声明在上面a和b）
            样例：v_name:= '小花';
            声明和赋值同时进行
            v_name varchar(500) := '小花'
	b、select 字段 into 变量 from 表名 进行赋值
			单个字段复制：
			select name into v_name from userinfo;
			使用条件：也是只限于a和b使用
			行记录复制：
			select * into v_user_row from userinfo;
			使用条件：也是只限于c使用
	c、execute immediate sql语句 into 变量 进行赋值
		 v_name varchar2(500);
		 v_name_sql varchar2(500) :='select name from userinfo where id=1';
		 execute immediate v_name_sql into v_name ;
		 语句含义是like执行v_name_sql 语句并将结果赋值给v_name。
````  
### 3.流程控制语句
- 条件控制语句 if then

````
    基本语法：
            IF 条件表达式1 THEN
            语句段1
            ELSIF 条件表达式2 THEN
            语句段2
            ......
            ELSIF 条件表达式n
            语句段n
            END IF;
	样例：如果flag_num=0则输出'早上好'，如果flag_num=1 则输出 '中午好'，否则输出'晚上好'，
	      declare 
	    	flag_num int:= 0;
	    begin
			if flag_num=0 then 
				dbms_output.put_line('早上好');
			elsif flag_num=1 then
				dbms_output.put_line('中午好');
			else
				dbms_output.put_line('晚上好');
			end if;
		end;
````  
- 条件控制语句 case

````
    基本语法：
            CASE
            WHEN 条件表达式1 THEN
            语句段1;
            WHEN 条件表达式2 THEN
            语句段2;
            ......
            ELSE
            语句段n;
            END CASE;
   样例：如果flag_num=0则输出'早上好'，如果flag_num=1 则输出 '中午好'，否则输出'晚上好'
	   	declare 
	   flag_num int:= 0;
	   begin
	   case 
	   when flag_num=0 then dbms_output.put_line('早上好');
	   when flag_num=1 then dbms_output.put_line('中午好');
	   else dbms_output.put_line('晚上好');
	  end case;
	  end;
````    
### 4.循环控制语句
- loop循环

````
        基本语法：
            LOOP
            EXIT [WHEN 条件表达式] --循环终止条件,为ture时，退出循环，否则再次执行循环体
            语句段;
            END LOOP;
		样例：
		declare 
		  i int:= 0;
		begin
		  loop
		      EXIT WHEN i > 3;
		      i:=i+1;
		      dbms_output.put_line(i);
	    end loop;
      end;
      语句解释：初始变量i为0，当i进入loop循环时，i>3则退出循环，i等于i+1,进行打印，然后依次循环，直至i>3时退出循环。
````    
- while循环

````
        基本语法：
	     		WHILE 条件表达式 LOOP
				语句段;
				END LOOP;
		样例：
		declare
			  i int := 0;
		begin
			  while i < 3 loop
			    i := i + 1;
			    dbms_output.put_line(i);
			  end loop;
		end;
		语句解释：初始变量i为0，当i进入loop循环时，i<3进入循环，i等于i+1,进行打印，然后依次循环，直至i不小于3时退出循环。
````    
- for循环
````
        基本语法：（注意：如加上reverse表示倒叙循环执行语句）
	     		FOR 循环变量 in [REVERSE] 初值表达式..终值表达式 LOOP
				语句段;
				END LOOP;
		样例：
		declare 
		  i int := 0;
		begin
		  for i IN reverse 1..5 loop
		    dbms_output.put_line(i);
		  end loop;
		end;
		语句解释：初始变量i为0，循环遍历1..5，for循环默认是递增，但是加上reverse 会变成递减，将5赋给i,打印i,直至将1..5全部循环遍历，结束循环，for循环只适用于已知循环次数的遍历
````    
### 5.游标
- 游标的定义：游标是SQL的一个内存工作区，由系统或用户以变量的形式定义。游标的作用就是用于临
  时存储从数据库中提取的数据块。在某些情况下，需要把数据从存放在磁盘的表中调到计
  算机内存中进行处理，最后将处理结果显示出来或最终写回数据库。这样数据处理的速度
  才会提高，否则频繁的磁盘数据交换会降低效率。
  我们可以把游标理解为一个用于封装多条数据的集合。因此对于集合的遍历操作，那么游标也可以使用
  
- 游标属性
  通过游标的属性，来了解游标的执行状态和结果，进而控制游标的执行操作。
  隐式游标可以使用名字SQL来访问，但要注意，通过SQL游标名总是只能访问前一个DML操作或单行SELECT操作的游标属性。
  通常在刚刚执行完操作之后，立即使用SQL游标名来访问属性。
  游标的属性有四种:
````    
    sql%found （布尔类型，默认值为null）和sql%notfound（布尔类型,默认值为null）：
        在执行DML语句之后，SQL%FOUND的属性值将是：
            .TRUE :INSERT --判断是否插入成功
            .TRUE :DELETE和UPDATE，至少有一行被DELETE或UPDATE.--判断修改和删除成功
            .TRUE :SELECT INTO至少返回一行  --判断查询成功
        当SQL%FOUND为TRUE时,SQL%NOTFOUND为FALSE。
	
	sql%rowcount(数值类型默认值为0)：
        在执行任何DML语句之前，SQL%ROWCOUNT的值都是NULL, 对于SELECT INTO语句，
         如果执行成功，SQL%ROWCOUNT的值为1,如果没有成功或者没有操作(如update、insert、
         delete为0条），SQL%ROWCOUNT的值为0， 而对于update和delete来说表示游标所检索
         数据库行的个数即更新或者删除的行数。
        
	sql%isopen(布尔类型):
	    SQL%ISOPEN是一个布尔值，如果游标打开，则为TRUE, 如果游标关闭，则为FALSE.对于隐式
         游标而言SQL%ISOPEN总是FALSE，这是因为隐式游标在DML语句执行时打开，结束时就立即
         关闭。
````    
    因为游标在运行DML语句时打开，完成后关闭，使用SQL%ISOPEN总是为false。所以隐式游标只使用SQL%FOUND,SQL%NOTFOUND,SQL%ROWCOUNT三个属性。

- 隐式游标
  DML操作和单行select语句会使用隐式游标，如insert、update、delete以及select...into...。
  使用样例：
  
````
1.创建一个包含name,age,addr三个字段的表

	create table userinfo(
						name varchar2(500);
						age number(20);
					 	addr varchar2(500);
	)   
2.编写插入的代码并用上面的游标属性进行监控

	declare
	begin
	  insert into userinfo (name,age,addr) values('zs',18,'sc');
	  dbms_output.put_line('游标所影响的行数：'||SQL%rowcount);
	  if SQL%NotFound then --没有找到上面执行的sql
	     dbms_output.put_line('SQL%NotFound：true');
	  else
	    dbms_output.put_line('SQL%NotFound：false');
	  end if;
	  if SQL%Found then --找到上面执行的sql
	    dbms_output.put_line('SQL%Found：true');
	  else
	    dbms_output.put_line('SQL%Found：false');
	  end if;
	  if SQL%isopen then  --游标是否打开
	    dbms_output.put_line('SQL%isopen：true');  
	  else
	    dbms_output.put_line('SQL%isopen：false'); 
	  end if;
	end;        
3.执行的结果：

	游标所影响的行数：1
	SQL%NotFound：false
	SQL%Found：true
	SQL%isopen：false

4.编写一个查询的代码对游标属性进行监控        

	declare
	v_info userinfo %ROWTYPE;
	begin
	  select * into v_info from TEST_TABLE where name ='zs';
	  dbms_output.put_line('name：'||v_info.name);
	  dbms_output.put_line('age：'||v_info.age);
	  dbms_output.put_line('addr：'||v_info.addr);
	  dbms_output.put_line('游标所影响的行数：'||SQL%rowcount);
	  if SQL%NotFound then 
	     dbms_output.put_line('SQL%NotFound：true');
	  else
	    dbms_output.put_line('SQL%NotFound：false');
	    
	  end if;
	  if SQL%Found then 
	    dbms_output.put_line('SQL%Found：true');
	  else
	    dbms_output.put_line('SQL%Found：false');
	  end if;
	  if SQL%isopen then   
	    dbms_output.put_line('SQL%isopen：true');  
	  else
	    dbms_output.put_line('SQL%isopen：false'); 
	  end if;
	end;        
6.查询的结果为：

	name：zs
	age：18
	addr：sc
	游标所影响的行数：1
	SQL%NotFound：false
	SQL%Found：true
	SQL%isopen：false    
````    
- 显示游标      
  1.游标声明：   
````
    1.带参数的声明：
        CURSOR 游标名 (参数1 数据类型,......)
        IS
        SELECT语句;
    2.不带参数的声明：
        CURSOR 游标名
        IS
        SELECT语句;
      注意：SELECT语句是对表或视图的查询语句，甚至也可以是联合查询。可以带WHERE条件、ORDER BY或GROUP BY等子句，但不能使用INTO子句。
````   
  2.打开游标    
````
    在可执行部分，按以下格式打开游标： 
    OPEN 游标名(参数1,.....) 				--存在参数
    或者
    OPEN 游标名 					--不存在参数
    打开游标时，SELECT语句的查询结果就被传送到了游标工作区。
````    
  3.提取数据    
````
    FETCH 游标名 INTO 变量; 
    游标打开后有一个指针指向数据区，FETCH语句一次返回指针所指的一行数据，要返回多行需重复
    执行，可以使用循环语句来实现。控制循环可以通过判断游标的属性来进行。
````    
  4.关闭游标    
````
close 游标名;  
显式游标打开后，必须显式地关闭。游标一旦关闭，游标占用的资源就被释放，游标变成无效，必须重新打开才能使用。
````    
### 6.学习用例      
- 经典样例      

````sql     
declare

v_name varchar2(500) :='zs'; --定义的常用变量

cursor cur_user(v_name varchar2)  --创建游标
is
select * from userinfo where name=v_name; --查询语句

v_user cur_user%rowtype;  --定义的行变量，用于接收游标中的数据

begin
  open cur_user(v_name); --打开游标
  loop     --循环开始
    fetch cur_user into v_user; --从游标中提取数据
     exit when  cur_user%notfound;--判断是游标中是否存在数据，不存在退出循环
     dbms_output.put_line('name：'||v_user.name);
	   dbms_output.put_line('age：'||v_user.age); 
	   dbms_output.put_line('addr：'||v_user.addr);
     dbms_output.put_line('------------');
  end loop;   
  close cur_user;  --关闭游标
end;        
````
