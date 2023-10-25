---
layout: default
title: postman
nav_order: 2
parent: csTools
---

- [postman请求时params中传输json格式的String参数](https://blog.csdn.net/weixin_51191538/article/details/127332119)
#### postman请求时params中传输json格式的String参数
- 在postman中json主要用到的符号[]{}可以用相关编码进行转义，如：    
  [->%5B    
  ]->%5D    
  {->%7B    
  }->%7D    
  在url中其他符号具体可以查看编码表。
  
#### 动态参数进行压测 
- 在postman中使用动态参数进行压测：
- 1.参数使用{{}}框起来，例如：字段APPLYNO，使用时{{APPLYNO}}
![img.png](img.png)
- 2.准备相关文档，文件格式為csv/txt、application/json文件 ,如1中只有一個字段applyno，則文檔中也只有一個字段
![img_1.png](img_1.png)
  
##### 动态参数进行压测补充，若数据开头为0，可以加上双引号“”（英文）
--1.txt文档
![img_3.png](img_3.png)  
--2.json文档
![img_4.png](img_4.png)
--3.csv文档  暂时不建议用 （适合用于大数据量）

#### 当postman中传参后中文乱码时，需要headers中在参数content-type 后加上charset=utf-8即可
![img_2.png](img_2.png)
