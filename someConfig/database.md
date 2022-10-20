---
layout: default
title: database
nav_order: 4
---

#mysql
spring:
datasource:
driver-class-name: com.mysql.cj.jdbc.Driver
url: jdbc:mysql://localhost:3306/${database_name}?serverTimezone=UTC
username: root
password: Ggdfm1210@zfl

#oracle
````bash
url: jdbc:oracle:thin:@192.168.147.128:1521:helowin
username: ggdfm
password: helowin
````
#mybatis-plus
mybatis-plus:
mapper-locations: classpath:mapper/**/*.xml
configuration:
log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
local-cache-scope: session

