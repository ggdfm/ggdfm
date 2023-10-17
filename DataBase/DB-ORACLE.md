---
layout: default
title: DB--ORACLE
nav_order: 3
parent: DATABASE
---

# ORACLE
{: .no_toc }

### /*+parallel（t，16）*/ 的使用    sql并行  
````
优势：强制启动并行进程，分配任务与系统资源，合并结果集。缩短计算时间。在大表查询等操作可以起到很好的效果。
劣势：比较消耗资源，不建议在系统超负荷下使用
select /*+parallel（t，16）*/ from t_consom t where app='';
t为表t_consom的别名
````

### 函数

- trunc
````
1.trunc(num,[int]);
  trunc(12.22) --去掉小数点后面部分,并且不进行四舍五入 结果为 12
  trunc(12.22,1) --保留1位小数，并且不进行四舍五入 结果为 12.2
  trunc(12.22,-1) --int参数可以使用负数，它的使用是从小数点位的左侧开始截取，并把截取的位数替换成0. 结果为 10
2.trunc(sysdate,'格式');
 格式可以为'yyyy','mm','dd' --- 年、月、星期的第一天
 对日期进行提取，返回当前日期的第一天。
````
- round(number,decimals) 
````
number：要进行四舍五入的数值
decimals：指明需保留小数点后面的位数。
          可选项，忽略它则截去所有的小数部分，并四舍五入。
          如果为正数则表示从小数点右边开始的位置进行四舍五入，
          如果为负数则表示从小数点开始左边的位数，相应整数数字用0填充，小数被去掉。
````
- instr(string1,string2)  在string1中查找string2
````
在Oracle/PLSQL中，instr函数返回要截取的字符串在源字符串中的位置。
只检索一次，也就是说从字符的开始到字符的结尾就结束。
例如：select instr('helloworld','l') from dual;返回 3 这是第一次出现l的位置。
    select instr('helloworld','lo') from dual;返回 4 这是第一次出现lo的位置。
````
- wm_concat()函数
````
 与Mysql中的group_concat()函数功能差不多
  实现行转换功能，将查询出的某一列值使用逗号进行隔开拼接
````
- REGEXP_SUBSTR(string,正则表达式，起始位置，获取通过正则表达式分割出来的第几组数据)
````
select REGEXP_SUBSTR('abcd_ef_g', '[^_]+', 2,1) from dual;
上示例表示由_分隔，从第二个位置开始的第一组数据。结果为 bcd
````    

## 使用shell脚本处理sql问题：
- 步骤：
````
1. 安装SQLPlus。SQLPlus是Oracle数据库的命令行界面，可以用于执行SQL查询和命令。您可以从Oracle官方网站下载SQLPlus。
2. 创建一个新的Shell脚本文件，并添加Shebang行。例如，如果您想使用Bash解释器，Shebang行应该是#!/bin/bash。
3. 在脚本中添加SQLPlus命令和语句来执行所需的任务。例如，如果您想运行一个SQL查询，可以使用sqlplus命令。如果您想将查询结果保存到文件中，可以使用spool命令。
````    
- 以下是一个简单的Shell脚本示例，用于从Oracle数据库中加载数据：  
````    
#!/bin/bash

# Set the Oracle environment variables
export ORACLE_HOME=/path/to/oracle/home
export PATH=$ORACLE_HOME/bin:$PATH

# Connect to the database and run the SQL*Loader command
sqlldr username/password@database control=loader.ctl log=loader.log

# Connect to the database and run the SQL*Plus command
sqlplus username/password@database @query.sql > output.txt

说明：在这个例子中，我们首先设置了Oracle环境变量，然后使用sqlldr命令运行了一个SQLLoader控制文件。
    然后，我们使用sqlplus命令运行了一个SQL查询，并将结果保存到一个文件中。
````    
- 
## sqlloader与sqlplus是Oracle数据库中的两个命令行实用程序。
- sqlldr用于将外部文件中的数据加载到Oracle数据库中，而sqlplus用于执行SQL命令和脚本。
- 要使用sqlldr，您需要创建一个控制文件（以.ctl结尾），指定数据文件的格式和目标表。以下是控制文件的示例：   
````    
LOAD DATA
INFILE 'data.txt'
INTO TABLE mytable
FIELDS TERMINATED BY ','
TRAILING NULLCOLS
(
  col1,
  col2,
  col3
)
说明：该控制文件指定数据在名为data.txt的文件中，并且应加载到名为mytable的表中。
  FIELDS TERMINATED BY ','子句指定数据文件中的字段由逗号分隔。
  TRAILING NULLCOLS子句表示未在控制文件中指定的任何列都应设置为null。
  括号中的列列表指定数据文件中的列的顺序和名称。
````

要运行sqlldr，您可以使用以下命令：
````
  sqlldr username/password@database control=control.ctl log=log.log
  说明：该命令指定要连接的用户名、密码和数据库，以及控制文件的名称和要创建的日志文件的名称。
````

要使用sqlplus，您可以创建一个包含要执行的命令的SQL脚本文件。以下是SQL脚本文件的示例：   
````    
SELECT * FROM mytable;
````    
要使用sqlplus运行此脚本，您可以使用以下命令：  
````    
sqlplus username/password@database @script.sql
说明：该命令指定要连接的用户名、密码和数据库，以及要执行的SQL脚本文件的名称。
````   

-  在oracle中，schema是数据库对象的集合；一个oracle用户对应一个schema，
   并且只能通过创建用户的方法创建schema，可以将schema称为user的别名，
   也就是schema名字同user名字对应并且相同
 