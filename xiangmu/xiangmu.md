---
layout: default
title: XiangMu
nav_order: 4
has_children: true
permalink: docs/xiangmu
---

## XiangMu
{: .no_toc}

### 项目使用框架 jdk版本及其他
### 项目部署前需要考虑的问题：
##### 版本兼容问题
````
1.jdk与各种数据库驱动兼容问题
2.jdk与tomcat兼容问题
3.jdk与maven版本兼容问题
4.jdk与各类其他jar包兼容问题
````
#### jar包冲突问题

### 解决jar包导入问题以及项目不能编译问题
````
一个ant的spring项目有多个子工程时，子工程不能被编译，jar包导不进去，
先File->Project Structure->library 查看jar是否导入，
如果没有则找到lib下的jar包，右击选择add library as 添加即可
有jar包之后，File->Project Structure->modules 添加module或者右图点击sources下的子项目，添加mark as行的sources。
````
### 测试时报错：compilation failed :internal java compiler error 需要修改堆内存或者清理内存
![修改IDEA启动项目时堆内存的步骤](img.png)