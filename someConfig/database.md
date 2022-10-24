---
layout: default
title: yml-config
nav_order: 4
---


## application.yml配置
## mysql
````bash
spring:
datasource:
driver-class-name: com.mysql.cj.jdbc.Driver
url: jdbc:mysql://localhost:3306/${database_name}?serverTimezone=UTC
username: root
password: Ggdfm1210@zfl
````

## oracle
````bash
url: jdbc:oracle:thin:@192.168.147.128:1521:helowin
username: ggdfm
password: helowin

#username: system
#password: ggdfm
````
# mybatis-plus
````bash
mybatis-plus:
mapper-locations: classpath:mapper/**/*.xml
configuration:
log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
local-cache-scope: session
````

---

### mysql语句

#### 授权语句
````
grant create session to 用户名;
grant create procedure to ggdfm;
grant create type to ggdfm;
grant create view to ggdfm;
grant debug connect session to ggdfm;
grant  debug any procedure to ggdfm;
grant create table to ggdfm;
````

#### 修改用户密码
````
alter user 用户名 identified by 新密码;
````
#### 


