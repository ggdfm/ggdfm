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
#注意：${project.basedir}的解释在buildTools/maven.md中。
#      maven 中 plugin 配置，不配置在本地可以找到 lib 下的 jar 包，但是编译后就不加载了，所以必须配置
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.0</version>
            <configuration>
                <encoding>UTF-8</encoding>
                <source>1.6</source>
                <target>1.6</target>
                <compilerArguments>
                  <extdirs>${project.basedir}/src/main/java/lib</extdirs>
                </compilerArguments>
            </configuration>
        </plugin>
    </plugins>
</build>
````
````yaml

spring:
#  application:
#    name: demo-admin
  datasource:
#    dbType: oracle 
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

````yaml
driverName: com.alibaba.oceanbase.jdbc.Driver
url: jdbc:oceanbase://${oceanbase.ip}:${oceanbase.port}/sys?uselocalsessionstate=true&useUnicode=true&characterEncoding=utf-8&rewriteBatchedStatements=true&allowMultiQueries=true
username: ${用户名@租户名#集群名 或者 集群名:租户名:用户名} # 例如 sys@obmysql#obtest
password: ${password}
initialSize: 5
maxIdle: 5  #最大空闲等待  出现报错Cannot get a connection, pool error Timeout waiting for idle object，可以尝试修改此参数值，例如50
maxActive: 20 #最大连接数据库连接数
maxWait: 30000 #连接池出借连接的最长期限
````

````yaml 
连接池的原理主要分为三部分：连接池的创建，连接池中连接的管理，连接池的关闭       
1，连接池大小及性能选项
maxActive：最主要参数，配置连接池同时能维持的最大连接数，如果客户端理论上需要100个连接，则这个值设为100。
maxIdle：如果客户端一段时间内不需要使用连接，又一直把所有连接池中的所有连接都维持在活动状态是很浪费资源的，所以maxIdle这个选项告诉tomcat，如果客户端没有需求，那么最多维持maxIdle个空闲连接。
minIdle：和maxIdle类似，maxIdle告诉tomcat最多维持多少个空闲连接，minIdle告诉tomcat即使客户端没有需求，也要至少维持多少个空闲连接，以应对客户端的突发需求。
initialSize：连接池启动时要初始化多少个连接，即使客户端这是没有需求，也会初始化空闲连接。
maxWait：连接池出借连接的最长期限，单位是毫秒，比如设为10000ms，客户端从连接池获取（借出）一个连接后，10000毫秒没有归还（return），则连接池会抛出异常。
maxAge：连接池中一个连接的寿命，连接池初始化一个连接后，会记下初始化的时间，以后每次出借，或有客户端归还这个连接时，连接池会检查 当前时间 - 初始化时间 > maxAge，如果超过maxAge，连接池会删除这个连接。

````        

````yaml        
对于连接池中的几个参数的管理策略：客户请求数据库连接时，先看数据库是否有空闲连接，有则直接分配连接给客户使用，
没有空闲连接则去查看是否已达最大连接数，未达到则创建一个连接给客户使用，已经达到最大连接数，则客户需要按设定的最大等待时间进行等待，
超出最大等待时间未有空闲连接，则抛出异常给客户
````


