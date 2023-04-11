---
layout: default
title: Spring
nav_order: 4
has_children: true
permalink: docs/spring
---

## Spring
{: .no_toc}

### AOP

### spring相关依赖
- spring-boot-starter-parent：spring-boot-starter-parent是一个特殊的Starter，其作用在于提供了一些Maven的默认配置，同时还提供了dependency-management，这样在开发的过程中引入其他依赖的时候不必输入版本号，方便了依赖的管理

````    
作用：
    1.定义了Java的编译版本，这里指的是1.8版。
    2.定义了工程的编码格式，使用的是UTF-8格式。
    3.继承了spring-boot-dependencies，这里头定义了很多依赖的版本，也正是因为继承了它，所以在有spring-boot-starter-parent的前提下我们可以不用写其他依赖的版本号。
    4.执行打包操作的相关配置。
    5.自动化的资源过滤，构建、编译项目时包括、忽略指定文件下满足特定要起的资源文件。
    6.配置了常见常用的插件，能满足大部分对插件的需求。
    7.针对application.properties和application.yml的资源过滤，包括通过profile定义的不同环境的配置文件，例如application-dev.properties和application-dev.yml这种。这在实际开发中蛮常用的，毕竟大伙干活时要区分开发、测试、生产环境
    
注意：spring-boot-starter-parent在properties中对工程的环境做了一些声明---java版本（1.8）、字符编码（UTF-8）
     当想要写其他版本的java工程时，需要在pom文件中再声明一次java版本，以此覆盖parent中规定的版本。

````    

- spring-boot-starter                核心启动器，包括自动配置支持，日志记录和YAML    
- spring-boot-starter-activemq            使用Apache ActiveMQ进行JMS消息传递的入门者
- spring-boot-starter-amqp            使用Spring AMQP和Rabbit MQ的入门者    
- spring-boot-starter-aop                使用Spring AOP和AspectJ进行面向方面编程的入门者    
- spring-boot-starter-artemis            使用Apache Artemis进行JMS消息传递的入门者    
- spring-boot-starter-batch            使用Spring Batch的入门者
- spring-boot-starter-cache            使用Spring Framework的缓存支持的初学者    
- spring-boot-starter-cloud-connectors        使用Spring Cloud Connectors的初学者简化了Cloud Foundry和Heroku等云平台中的服务连接
- spring-boot-starter-data-cassandra        使用Cassandra分布式数据库和Spring Data Cassandra的入门者
- spring-boot-starter-data-cassandra-reactive    使用Cassandra分布式数据库和Spring Data Cassandra Reactive的入门者
- spring-boot-starter-data-couchbase        使用Couchbase面向文档的数据库和Spring Data Couchbase的入门者
- spring-boot-starter-data-couchbase-reactive    使用Couchbase面向文档的数据库和Spring Data Couchbase Reactive的入门者
- spring-boot-starter-data-elasticsearch        使用Elasticsearch搜索和分析引擎以及Spring Data Elasticsearch的初学者
- spring-boot-starter-data-jdbc            使用Spring Data JDBC的入门者
- spring-boot-starter-data-jpa            将Spring Data JPA与Hibernate一起使用的初学者
- spring-boot-starter-data-ldap            使用Spring Data LDAP的入门者
- spring-boot-starter-data-mongodb        使用MongoDB面向文档的数据库和Spring Data MongoDB的初学者
- spring-boot-starter-data-mongodb-reactive    使用MongoDB面向文档的数据库和Spring Data MongoDB Reactive的入门者
- spring-boot-starter-data-neo4j            使用Neo4j图形数据库和Spring Data Neo4j的入门者
- spring-boot-starter-data-redis            在Spring Data Redis和Lettuce客户端上使用Redis键值数据存储的初学者
- spring-boot-starter-data-redis-reactive        使用带有Spring Data Redis被动的Redis键值数据存储和Lettuce客户端的入门者
- spring-boot-starter-data-rest            使用Spring Data REST通过REST公开Spring Data存储库的入门者
- spring-boot-starter-data-solr            在Spring Data Solr中使用Apache Solr搜索平台的初学者
- spring-boot-starter-freemarker            使用FreeMarker视图构建MVC Web应用程序的入门者
- spring-boot-starter-groovy-templates        使用Groovy模板视图构建MVC Web应用程序的入门者
- spring-boot-starter-hateoas            使用Spring MVC和Spring HATEOAS构建基于超媒体的RESTful Web应用程序的入门者
- spring-boot-starter-integration            使用Spring Integration的入门者
- spring-boot-starter-jdbc            将JDBC与HikariCP连接池一起使用的入门者
- spring-boot-starter-jersey            使用JAX-RS和Jersey构建RESTful Web应用程序的初学者。替代spring-boot-starter-web
- spring-boot-starter-jooq            使用jOOQ访问SQL数据库的初学者。替代spring-boot-starter-data-jpa或spring-boot-starter-jdbc
- spring-boot-starter-json            阅读和写作json的初学者
- spring-boot-starter-jta-atomikos        使用Atomikos进行JTA交易的入门者
- spring-boot-starter-jta-bitronix        使用Bitronix进行JTA事务的入门者
- spring-boot-starter-jta-narayana        Spring Boot Narayana JTA 启动器
- spring-boot-starter-mail            使用Java Mail和Spring Framework的电子邮件发送支持的入门者
- spring-boot-starter-mustache            使用Mustache视图构建Web应用程序的入门者
- spring-boot-starter-oauth2-client        使用Spring Security的OAuth2 / OpenID Connect客户端功能的入门者
- spring-boot-starter-oauth2-resource-server    使用Spring Security的OAuth2资源服务器功能的入门者
- spring-boot-starter-quartz            使用Quartz调度程序的入门者    
- spring-boot-starter-security            使用Spring Security的入门者
- spring-boot-starter-test            使用JUnit，Hamcrest和Mockito等库来测试Spring Boot应用程序的初学者    
- spring-boot-starter-thymeleaf            使用Thymeleaf视图构建MVC Web应用程序的入门者
- spring-boot-starter-validation            使用Java Bean Validation和Hibernate Validator的初学者
- spring-boot-starter-web                使用Spring MVC构建Web（包括RESTful）应用程序的入门者。使用Tomcat作为默认嵌入式容器
- spring-boot-starter-web-services        使用Spring Web Services的入门者
- spring-boot-starter-webflux            使用Spring Framework的Reactive Web支持构建WebFlux应用程序的初学者
- spring-boot-starter-websocket            使用Spring Framework的WebSocket支持构建WebSocket应用程序的初学者
- spring-boot-starter-jetty            使用Jetty作为嵌入式servlet容器的入门。替代spring-boot-starter-tomcat
- spring-boot-starter-log4j2            使用Log4j2进行日志记录的入门。替代spring-boot-starter-logging
- spring-boot-starter-logging            使用Logback进行日志记录的入门。默认日志启动器
- spring-boot-starter-reactor-netty        使用Reactor Netty作为嵌入式响应式HTTP服务器的入门者。
- spring-boot-starter-tomcat            使用Tomcat作为嵌入式servlet容器的入门者。使用的默认servlet容器启动器spring-boot-starter-web
- spring-boot-starter-undertow            使用Undertow作为嵌入式servlet容器的入门者。替代spring-boot-starter-tomcat



