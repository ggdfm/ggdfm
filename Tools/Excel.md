---
layout: default
title: Excel
nav_order: 1
parent: Tools
---

## 帮助更快java开发

### 1.将数据库类型与所在位置的字符串进行匹配，匹配成功将转换成对应的java类型
````excel
=IF(COUNTIF('目标所在位置',"*number*")>0,"BigDecimal",
IF(COUNTIF('目标所在位置',"*varchar*")>0,"String",
IF(COUNTIF('目标所在位置',"*datetime*")>0,"Date",
IF(COUNTIF('目标所在位置',"*decimal*")>0,"BigDecimal",
"不能识别的类型"))))
````

### 2.将形如 BATCH_NO 转换成 batchNo形式。
#### SUBSTITUTE -> 例如 SUBSTITUTE(”BATCH_NO“,"_","")就是将空字符""替代"_"
#### PROPER -> 将指定的文本中首字母以及任何非字母后的第一个字母大写，其他小写
````excel
=LOWER(LEFT(A18,1))&RIGHT(SUBSTITUTE(PROPER(A18),"_",""),LEN(SUBSTITUTE(PROPER(A18),"_",""))-1)
````

### 3.两组数据进行比对
![img.png](img.png) 
````excel   
=VLOOKUP(A1,B1:C2,2,false)
函数含义：将A1的数据在B1-C2的区域查找数据，若有相同的数据，则将区域的第二列该行数据返回。
fasle 为精确查询
true为近似查询

与另一个sheet页数据比对
=vlookup(A1,'sheet2'!B:C,2,false)
解释：将A1列的数据与sheet2页的B-C区域查找数据，若有相同数据，返回第二列

近似查询
=vlookup("*"&A1&"*",B:C,2,0)
=vlookup("*"&A1&"*",B:C,2,false)
解释：加上*相当于sql中的like '%'。
````

### 4.判断  
````excel
=iferror(VLOOKUP(A1,B1:C2,2,false),false)
含义：如果vlookup函数没找到数据，则返回false（返回值可以自定义）
````

-excel连字符&
### 5.subtotal

### match 返回目标在行或列的位置
````excel
=match(查找目标,查找区域,【查找方式】)
查找方式：0为精确查找，1为近似查找
可以与vlookup联用，用于1对多查询

````

-excel 快捷键
````
ctrl+D  填充，与第一行一致
````