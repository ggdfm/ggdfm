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
#### SUBSTITUTE -> 使用新字符替换旧字符   
#### PROPER -> 将指定的字符串首字母大写
````excel
=LOWER(LEFT((SUBSTITUTE((PROPER(B1)),"_","")),1))
&RIGHT((SUBSTITUTE((PROPER(B1)),"_","")),LEN((SUBSTITUTE((PROPER(B1)),"_","")))-1)
````