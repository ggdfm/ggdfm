---
layout: default
title: Config-DB
nav_order: 2
parent: Config
---

# Config-DB
{: .no_toc }


## application.yml配置
## mysql
```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/${database_name}?serverTimezone=UTC
    username: root
    password: Ggdfm1210@zfl
```

## oracle
````yaml
spring:
  datasource:
    driver-class-name: oracle.jdbc.driver.OracleDriver
    url: jdbc:oracle:thin:@192.168.147.128:1521:helowin
    username: ggdfm
    password: helowin

#username: system
#password: ggdfm

驱动需要引入依赖：
  <dependency>
      <groupId>com.oracle.ojdbc</groupId>
      <artifactId>ojdbc8</artifactId>
  </dependency>
````

## OceanBase
- OceanBase由于在maven官网未找到，所以需要本地引入（下载jar，放在lib文件夹下），驱动器在jar包中
````yaml
#依赖：
<dependency>
    <groupId>com.oceanbase</groupId>
    <artifactId>oceanbase-client</artifactId>
    <version>2.2.7.2</version>
    <scope>system</scope>
    <systemPath>${project.basedir}/lib/oceanbase-client-2.2.7.2.jar</systemPath>
</dependency>
````
````yaml

spring:
#  application:
#    name: demo-admin
  datasource:
#    dbType: oracle #oceanBase
#    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
    driver-class-name: com.oceanbase.jdbc.Driver
#    filters: stat,wall,log4j
#    initialSize: 5
#    maxActive: 20
#    maxPoolPreparedStatementPerConnectionSize: 20
#    maxWait: 60000
#    minEvictableIdleTimeMillis: 300000
#    minIdle: 5
#    poolPreparedStatements: true
#    testOnBorrow: false
#    testOnReturn: false
#    testWhileIdle: true
#    timeBetweenEvictionRunsMillis: 60000
#    validationQuery: SELECT 'x' FROM DUAL
    type: com.alibaba.druid.pool.DruidDataSource
    url: jdbc:oceanbase://${oceanbase.ip}:${oceanbase.port}/sys?useUnicode=true&characterEncoding=utf-8&rewriteBatchedStatements=true&allowMultiQueries=true
    username: ${用户名@租户名#集群名 或者 集群名:租户名:用户名} # 例如 sys@obmysql#obtest
    password: ${password}
#    useGlobalDataSourceStat: true
#    druid:
#      validationQuery: SELECT 'x' FROM DUAL
````
# mybatis-plus
````yaml
mybatis-plus:
  mapper-locations: classpath:mapper/**/*.xml
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    local-cache-scope: session
````

## 在application.yml文件中指定项目运行时使用的yml环境，多环境开发的其他环境格式：application-{profile}.yml
````yaml
spring:
  profiles:
    active: local
````
### 可以通过Environment获得当前项目使用环境
````
public static void main(String[] args) {
        try {
            ConfigurableApplicationContext application = SpringApplication.run(启动类.class, args);
            Environment env = application.getEnvironment();
            System.out.println("环境配置为:"+env.getActiveProfiles()[0]);
            String host = InetAddress.getLocalHost().getHostAddress();
            System.out.println("ip地址为："+host);
            String port = env.getProperty("server.port");
        } catch (Exception e) {
            log.error("", e);
        }
    }
````
---



