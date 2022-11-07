---
layout: default
title: yml-config
nav_order: 4
---


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



