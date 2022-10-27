---
layout: default
title: XxlJob
nav_order: 3
parent: Tools
---

### [xxljob 任务调度管理器](https://www.xuxueli.com/xxl-job/#2.1%20初始化“调度数据库”)

### 一、
#### 1.初始化“调度数据库”
##### 在官网下载项目源码并解压，获取“调度数据库初始化sql脚本”并执行，脚本位置为：
````
/xxl-job/doc/db/tables_xxl_job.sql
````
#### 2.编译源码（下载并解压的xxl-job源码）

#### 3.配置部署“调度中心”    
##### 调度中心配置文件地址：
````
/xxl-job/xxl-job-admin/src/main/resources/application.properties        
配置jdbc连接，数据库与1中数据库初始化脚本的数据库一致
````

### 二、部署项目  （xxl-job-admin）
#### 将项目编译打包并部署。
#### 打包  maven--clean--package    
#### 部署  包目录，cmd -- （java -jar 文件名.后缀）
#### 调度中心访问地址：http://localhost:8080/xxl-job-admin (该地址执行器将会使用到，作为回调地址)
#### 默认登录账号 “admin/123456” 。先数据库执行添加角色脚本

### 三、调度中心集群（可选）：调度中心支持集群部署，提升调度系统容灾和可用性。
#### 1.要求：DB配置保持一致；集群机器时钟保持一致；建议使用nginx为调度中心集群做负载均衡，分配域名。调度中心访问、执行器回调配置、调用API服务等操作均通过该域名进行。
####  2.使用Docker镜像方式搭建调度中心：
````
下载镜像
// Docker地址：https://hub.docker.com/r/xuxueli/xxl-job-admin/ (建议指定版本号)           
docker pull xuxueli/xxl-job-admin        
创建容器并运行    
docker run -p 8080:8080 -v /tmp:/data/applogs --name xxl-job-admin  -d xuxueli/xxl-job-admin:{指定版本}         
/**         
* 如需自定义 mysql 等配置，可通过 "-e PARAMS" 指定，参数格式 PARAMS="--key=value  --key2=value2" ；         
* 配置项参考文件：/xxl-job/xxl-job-admin/src/main/resources/application.properties          
* 如需自定义 JVM内存参数 等配置，可通过 "-e JAVA_OPTS" 指定，参数格式 JAVA_OPTS="-Xmx512m" ；           
*/          
docker run -e PARAMS="--spring.datasource.url=jdbc:mysql://127.0.0.1:3306/xxl_job?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai" -p 8080:8080 -v /tmp:/data/applogs --name xxl-job-admin  -d xuxueli/xxl-job-admin:{指定版本}             
````
#### 3.配置部署 “执行器项目”  (xxl-job-executor-sample-springboot)
##### 作用：负责接收“调度中心”的调度并执行；可直接部署执行器，也可以将执行器集成到现有业务项目中。
##### 3.1引入maven依赖
````
<dependency>
    <groupId>com.xuxueli</groupId>
    <artifactId>xxl-job-core</artifactId>
    <version>${最新稳定版本}</version>
</dependency>
````  
##### 3.2执行器配置
````
执行器在xxljob的位置
/xxl-job/xxl-job-executor-samples/xxl-job-executor-sample-springboot/src/main/resources/application.properties
````
#### 或者直接在所用项目中配置
````
xxl:
  job:
    admin:
      addresses: http://37.220.51.172:8083/xxl-job-admin  #xxljob调度中心部署 例如：http://127.0.0.1:8080/xxl-job-admin
    executor:
      address:
      appname: shb-executor	 #xxljob配置的执行器名称，
      ip: 37.220.51.172 #执行器IP，默认为空表示自动获取IP
      port: 9999 #xxljob配置的端口号，默认为9999
      logpath: /data/xxl-job/jobhandler  #执行器运行日志文件存储磁盘路径
      logretentiondays: -1  #调度中心日志表数据保存天数，过期日志自动清理；限制大于等于7时生效，否则, 如-1，关闭自动清理功能
    accessToken: default_token #调度中心通讯TOKEN [选填]：非空时启用
````

##### 加上配置类
````
@Configuration
public class XxlJobConfig {
    private Logger logger = LoggerFactory.getLogger(XxlJobConfig.class);

    @Value("${xxl.job.admin.addresses}")
    private String adminAddresses;

    @Value("${xxl.job.accessToken}")
    private String accessToken;

    @Value("${xxl.job.executor.appname}")
    private String appname;

    @Value("${xxl.job.executor.address}")
    private String address;

    @Value("${xxl.job.executor.ip}")
    private String ip;

    @Value("${xxl.job.executor.port}")
    private int port;

    @Value("${xxl.job.executor.logpath}")
    private String logPath;

    @Value("${xxl.job.executor.logretentiondays}")
    private int logRetentionDays;


    @Bean
    public XxlJobSpringExecutor xxlJobExecutor() {
        logger.info(">>>>>>>>>>> xxl-job config init.");
        XxlJobSpringExecutor xxlJobSpringExecutor = new XxlJobSpringExecutor();
        xxlJobSpringExecutor.setAdminAddresses(adminAddresses);
        xxlJobSpringExecutor.setAppname(appname);
        xxlJobSpringExecutor.setAddress(address);
        xxlJobSpringExecutor.setIp(ip);
        xxlJobSpringExecutor.setPort(port);
        xxlJobSpringExecutor.setAccessToken(accessToken);
        xxlJobSpringExecutor.setLogPath(logPath);
        xxlJobSpringExecutor.setLogRetentionDays(logRetentionDays);

        return xxlJobSpringExecutor;
    }

    /**
     * 针对多网卡、容器内部署等情况，可借助 "spring-cloud-commons" 提供的 "InetUtils" 组件灵活定制注册IP；
     *
     *      1、引入依赖：
     *          <dependency>
     *             <groupId>org.springframework.cloud</groupId>
     *             <artifactId>spring-cloud-commons</artifactId>
     *             <version>${version}</version>
     *         </dependency>
     *
     *      2、配置文件，或者容器启动变量
     *          spring.cloud.inetutils.preferred-networks: 'xxx.xxx.xxx.'
     *
     *      3、获取IP
     *          String ip_ = inetUtils.findFirstNonLoopbackHostInfo().getIpAddress();
     */

````
##### 在使用的方法上使用注解  @XxlJob("JobHandler名称")













#### 