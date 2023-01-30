---
layout: default
title: DB--ORACLE
nav_order: 3
parent: DATABASE
---

# ORACLE
{: .no_toc }

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
 