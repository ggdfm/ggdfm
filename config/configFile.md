---
layout: default
title: 各种配置文件类型
nav_order: 4
permalink: docs/config
---

## 配置文件类型
{: .no_toc}

### xml与properties
- 可以在xml文件中调用properties文件信息，只需要添加以下代码：
````
- xml与properties在同一个目录添加一个文件时：
  <context:property-placeholder location="jdbc.properties"/>
  在不同的目录时：
  <context:property-placeholder location="classpath:路径/jdbc.properties"/>
- 多个properties文件被一个xml文件使用时：
    - 具体的文件
    <context:property-placeholder location="p.properties,pbp.properties"/>
    - 不具体文件
    <context:property-placeholder location="*.properties"/>
````    
### xml与xml之间的调用
````
    <import resource="classpath:路径/db-config.xml" />
    <import resource="classpath*:db-config.xml" />
    <import resource="springmvc-servlet.xml" />
````