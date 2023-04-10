---
layout: default
title: Config-Log4j
nav_order: 3
parent: Config
---

# Log4j
{: .no_toc }

### 日志也可以使用lombok的@Slf4j，安装lombok插件，引入lombok依赖，slf4j-api依赖

### 1.maven项目需要引入log4j的依赖

#### log4j的注意事项
- log4j.properties的配置文件需放在src（或者WEB/INFO/classes）目录下，启动tomcat进程时log4j引擎也会随之启动（也就是log4j会自动初始化）。
- 如果log4j.properties配置文件放在别的目录，则需要应用程序负责初始化log4j，如spring提供了一个初始化log4j的监听类，当然你也可以自已写个servlet初始化log4j。
- log4j.properties里面的配置key=value时，value后面一定不能有空格，否则该项配置不能生效，因为log4j没有处理value后面的空格。
### 2.创建log4j.properties配置文件，示例:
````
## set log levels - for more verbose logging change 'INFO' to 'DEBUG' ##
log4j.rootLogger = info,stdout,fileout  #此句为将等级为INFO的日志信息输出到stdout和R这两个目的地，stdout和R的定义在下面的代码，可以任意起名。
### log4j.addivity.org.apache=true


# -------Logger--------
#log4j.logger.com.cpic=DEBUG
log4j.Logger.com.cpic=INFO,FILEOUT
log4j.logger.org=info
log4j.Logger.com.bstek=WARN
log4j.logger.net=WARN
## logger是日志组件的主要概念，用于指定不同的包使用不同的日志级别，或者指定一个logger组件，以便在使用的时候可以使用getLogger(组件名)的方式获取该logger的定义。
## logger中主要指定一个日志级别，以及数个具体的记录器appender，这样程序中就可以选用哪些记录器进行记录日志，以及记录什么级别的日志。
## 两种使用方法：public static Logger logger1 = Logger.getLogger(类名.class);
## public static Logger logger2 = Logger.getLogger("配置文件中指定的名称，例如31行的名称：net");
appender是具体执行记录日志到文件或者终端的组件。
### -------控制台--------
log4j.appender.stdout=org.apache.log4j.ConsoleAppender #此句为定义名为stdout的输出端是哪种类型
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout #此句为定义名为stdout的输出端的layout是哪种类型
log4j.appender.stdout.layout.ConversionPattern=TTTT: %p %-d{yyyy-MM-dd HH:mm:ss} (%c:%L) - %m%n
log4j.appender.stdout.encoding=utf-8


### -------文件输出：按照文件大小生成新文件--------
log4j.appender.fileout=org.apache.log4j.RollingFileAppender
log4j.appender.fileout.File=/app/logs/cfmslog/cfms.log #输出端的文件名
#log4j.appender.fileout.File=E://applog//cibslog
log4j.appender.fileout.MaxFileSize=10240KB
log4j.appender.fileout.MaxBackupIndex=20
log4j.appender.fileout.layout=org.apache.log4j.PatternLayout
log4j.appender.fileout.layout.ConversionPattern=TTTT:%-d{yyyy-MM-dd HH:mm:ss} %p (%c:%L) - %m%n
log4j.appender.fileout.encoding=utf-8
````
- 以上示例字段讲解
````
- 输出端类型：
    org.apache.log4j.ConsoleAppender（控制台），
    org.apache.log4j.FileAppender（文件），
    org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件），
    org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件）
    org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）
- 输出端的layout类型：
    org.apache.log4j.HTMLLayout（以HTML表格形式布局），
    org.apache.log4j.PatternLayout（可以灵活地指定布局模式），
    org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串），
    org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息）
- log4j.appender.stdout.layout.ConversionPattern= [QC] %p [%t] %C.%M(%L) | %m%n
    如果使用pattern布局就要指定的打印信息的具体格式ConversionPattern，打印参数如下：
    %m 输出代码中指定的消息；
    %M 输出打印该条日志的方法名；
    %p 输出优先级，即DEBUG，INFO，WARN，ERROR，FATAL；
    %r 输出自应用启动到输出该log信息耗费的毫秒数；
    %c 输出所属的类目，通常就是所在类的全名；
    %t 输出产生该日志事件的线程名；
    %n 输出一个回车换行符，Windows平台为"rn”，Unix平台为"n”；
    %d 输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，比如：%d{yyyy-MM-dd HH:mm:ss,SSS}，输出类似：2002-10-18 22:10:28,921；
    %l 输出日志事件的发生位置，及在代码中的行数；
    [QC]是log信息的开头，可以为任意字符，一般为项目简称。
    输出的信息
    具体格式示例：[TS] DEBUG [main] AbstractBeanFactory.getBean(189) | Returning cached instance of singleton bean 'MyAutoProxy'
````
### 3.
````
在要输出的日志的类中加入相关语句：
    static Logger logger = Logger.getLogger(LogDemo.class); //LogDemo为相关的类
在相应的方法中：
    if (logger.isDebugEnabled()){
    logger.debug(“System …..”);
    }
````