---
layout: default
title: DATABASE
nav_order: 4
has_children: true
permalink: docs/Database
---

## 数据库
{: .no_toc }

[sql优化](https://blog.csdn.net/qq_18237141/article/details/108463556)

#### 对表分区
- 分区表是指将一个大表拆分成多个小表，每个小表称为一个分区，分区表中包含多个分区，每个分区对应一个独立的物理存储空间。
- partition的用法
````    
1. 分区类型 ：范围分区、列表分区、哈希分区
1-1：范围分区
create table t_consum (
id varchar(10),
age number )
partition by range(age)(
partition a1 value less then 12,
partition a2 value less then 18,
partition a3 value less then (maxvalue)
)
1-2：哈希分区：创建时可以显示的指定每个分区名称也为tablespace表空间，也可以仅指定哈希分区的数量，oracle会自动生成分区名字
create ....
partition by hash(id)(
partition a1 tablespace  tbs1,
partition a2 tablespace  tbs2
)
1-3：列表分区
create....
partition by list(age)(
partition a1 value  12,
partition a2 value  18,
partition a3 value  (default)
)
2.partition的使用  
select * from t_consum partition(a1) ;
select * from t_consum partition(a1) a;--表有别名也要写在分区后

3.修改分区 列表分区
alter table t_consum modify partition(a1) add value(13);
alter table t_consum modify partition(a1) drop value(13);
````

#### 添加索引

#### 回表操作：二次查询操作
````    
1.聚族索引(主键索引)：根据主键查询的数据没有回表操作，因为该索引节点包含数据整条记录值
2.非聚族索引：非主键索引，该索引节点包含主键字段的值与该索引的值，
当查询该索引以外的值，条件为非主键索引时，会根据非主键索引获得主键值，再根据主键值二次查询获得该索引以外的值。
例子：a为主键索引，b为普通索引，c为其他字段
select * from table where a=1;--主键查询，获得所有值
select * from table where b=1;--其他索引查询，先获得主键，再回表根据主键获得所有值
3.覆盖索引的作用：避免回表操作
创建索引为联合索引，根据联合索引内的第一个索引值查询联合索引里的字段，就可以直接再索引节点获取值，不用二次查询。
````

#### DDL、DML、DCL
- DDL（数据定义语言）：对表进行操作，如Create、Drop、Alter
- DML（数据操作语言）:对数据进行操作，如Insert、Select、Update、Delete
- DCL（数据控制语言）：对数据库对象进行操作，
  如Grant（授予权限）、deny（拒绝用户或组对数据库对象的权限）、
  revoke（撤销权限）、commit（事务提交）、
  savepoint（保存点（savepoint）是事务过程中的一个逻辑点，用于取消部分事务，当结束事务时，会自动的删除该事务中所定义的所有保存点。当执行rollback时，通过指定保存点可以回退到指定的点。）、
  RollBack（事务回滚）

  补充：release savepoint 保存点名;该命令用来删除创建的保存点

#### 数据库无缝切换
- [数据库方言-数据库切换](http://www.manongjc.com/detail/64-vitbczditlzxdos.html)
#### druid-1.0.29.jar（或其他版本）含有加密解密功能，提供连接池的功能
- 加密可以在druid-1.0.31.jar的目录下，使用cmd，
  输入命令：java -cp druid-1.0.31.jar com.alibaba.druid.filter.config.ConfigTools your_password  
#### 使用注解注入的方法配置DataSource，并使用properties配置数据库信息，运用druid加密解密
````
- 1.properties文件（以oracle为例子）：
jdbc.driverName = oracle.jdbc.driver.OracleDriver
jdbc.url=jdbc:oracle:thin:@***.***.**.***:****:orcl
jdbc.username=****
jdbc.password=******************************************
jdbc.publicKey=**************************************************
- 2.java配置文件：
package com.example.spring_tomcat_demo.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;
import java.io.FileInputStream;
import java.io.IOException;
import java.sql.SQLException;
import java.util.Properties;

@Configuration
public class DBConfig {

    Properties properties = new Properties();

    {
        try {
            properties.load(new FileInputStream("src/main/resources/jdbc.properties"));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //换连接池  DBCP换成Druid
    @Bean
    public DataSource getDataSource() throws SQLException {
        DruidDataSource dataSource = new DruidDataSource();
//        //开启解密
        properties.setProperty("config.decrypt","true");
//        //添加公钥
        properties.setProperty("config.decrypt.key",properties.getProperty("jdbc.publicKey"));
        //配置监控统计拦截（解密需要）
        dataSource.setFilters("config");
        dataSource.setUrl(properties.getProperty("jdbc.url"));
        dataSource.setUsername(properties.getProperty("jdbc.username"));
        dataSource.setPassword(properties.getProperty("jdbc.password"));
//        dataSource.setPassword("bvis123");
        dataSource.setDriverClassName(properties.getProperty("jdbc.driverName"));
        dataSource.setConnectProperties(properties);
        System.out.println("===========>"+properties.toString());
        return dataSource;
    }

}
- 3.测试
    @Test
    public void test_32(){
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(DBConfig.class);
        DataSource bean = context.getBean(DataSource.class);
        String sql = "select * from RISK_SURVEY_QUALITY_LIST where CUSTOMER_NAME = '李四'";
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(bean);
        List<Map<String, Object>> mapList = jdbcTemplate.queryForList(sql);
        System.out.println(mapList.toString());
    }
````
#### 数据库配置信息参数及含义
````    
1.initial-size:是数据库连接池初始化的时候连接初始化的数量
2.maxActive:数据库连接池的最大连接数，一般是根据系统并发量来设置，设置为0表示无限制
3.maxIdle:表示数据库的最大空闲连接数，表示即使没有数据库连接时，连接池中的连接数量最大也可以保持在maxIdle,设置为0表示无限制
4.minIdle:表述数据库的最小空闲连接数，表示数据库中连接池中应该保存的最小的连接个数，当小于这个个数时，数据库会创建连接补充进去
5.maxWait:等待连接的最长时间，单位为毫秒，当超过这个时间时系统会抛异常，设置为-1时表示无限制等待，直到超时为止
````

#### 数据库手动回滚
````yaml
首先执行 set autocommit ='0';的语句，禁止自动提交功能
之后执行insert/update/delete等语句，此时数据不会立即生效，需要手动提交或回滚
需要回滚则执行RollBack;回滚未提交的语句
需要提交则执行Commit;提交未提交的语句
````
#### 数据库中数据冗余是指一个字段在多个表中出现容易造成信息错误，适当的使用外键可以使得数据冗余和异常降到最低。有时候为了效率和便利会故意设计冗余
````
冗余字段应遵循：不能为频繁修改的字段，不是varchar超长字段
````

##### 批处理频率如: sysdate+1/2880  1为一天的时间。2880=24*60*2; 1天=1*24*60*60秒 ;所以频率为30秒一次;


##### sql的条件a.username=b.username 与 a.username=b.username(+)的区别   
````  
a.username=b.username表示a表username字段在b表username字段里有则记录被选中。
a.username=b.username(+)表示a表与b表的username字段相同被选中以及b表有，a表无（null）时，记录也会被选中。
````  
###### 修改序列号
###### 序列号查看  
````yaml      
select ccc_seqNo.nextval from dual;//获取下一个序列号
select ccc_seqNo.currval from dual;//获取当前序列号
````
###### 一、通过查看表的序列值，以及当前序列号来修改序列
````
--说明：ccc_seqNo：序列字段。
alter sequence ccc_seqNo increment by 1000;//序列值跨越度=表中该字段最大值-当前序列值。
select ccc_seqNo.nextval from dual;//执行成功之后，序列值比字段最大值大
alter sequence ccc_seqNo increment by 1;//将跨越度重新改成1
````
###### 二、通过删表，重新创建序列来修改序列号
````yaml  
drop sequence ccc_seqNo;//删除序列
create sequence ccc_seqNo minvalue 1 maxvalue 9999999999999999 
       start with 3123213 increment by 1 nocache ; //创建序列，最小值为1，最大值为9999999999999999，以3123213开始，差值为1增长
````  

###### 并发与锁表
````yaml
- 并发操作
OB的update操作是先删除原数据，再插入新数据
所以情况可能为两台并发操作的机子操作一条数据，两边都查到了相同数据，但是一台机器先进行修改，导致另一台再做修改操作时找不到数据而报错。

解决方式可以为锁表，将事务隔离级别提到最高，但是影响性能

同一事务的连续读操作可能会看到不一样的数据，这是因为在同一事务中，
  每次读操作都会读取到最新提交的数据版本，而其他的并发事务可能会在连续读操作之间进行数据更改
````

#####
````    
因为Oracle中支持多个事务并发执行，所以会出现下面的数据异常

（1）脏读

脏读概念：当一个事务修改数据时，另一事务读取了该数据，但是第一个事务由于某种原因取消对数据修改，使数据返回了原状态，这时第二个事务读取的数据与数据库中数据不一致，这就叫脏读。

如：事务T1修改了一条数据，但是还未提交，事务T2恰好读取到了这条修改后了的数据，此时T1将事务回滚，这个时候T2读取到的数据就是脏数据。
问：为什么事务未提交，另一个事务就可以读到已修改的数据？
答：因为oracle与mysql都是基于文件系统进行数据存储的，即数据是持久到文件系统的，数据库内含内存池，完成对磁盘数据的缓存，提高读写效率。
内存池是对所有线程共享的，事务未提交，但是此时数据已存储到共享内存中，所以其他事务可以看见未修改数据。
想要解决这个问题可以将隔离级别提高，但是会导致效率变慢。

（2）不可重复读

不可重复读概念：是指一个事务读取数据库中的数据后，另一个事务则更新了数据，当第一个事务再次读取其中的数据时，就会发现数据已经发生了改变，这就是不可重复读取。不可重复读取所导致的结果就是一个事务前后两次读取的数据不相同。

如：事务T1读取一行记录，紧接着事务T2修改了T1刚刚读取的记录，然后T1再次查询，发现与第一次读取的记录不同。

（3）幻读

幻读概念：如果一个事务基于某个条件读取数据后，另一个事务则更新了同一个表中的数据，这时第一个事务再次读取数据时，根据搜索的条件返回了不同的行，这就是幻读。

如：事务T1读取一条指定where条件的语句，返回结果集。此时事务T2插入一行新记录，恰好满足T1的where条件。然后T1使用相同的条件再次查询，结果集中可以看到T2插入的记录，这条新纪录就是幻读。

事务中遇到的这些异常与事务的隔离性设置有关，事务的隔离性设置越多，异常就出现的越少，但并发效果就越低，事务的隔离性设置越少，异常出现的越多，并发效果越高。



````

#### 函数
#### connect by  
- 循环的函数  下列sql表示上个月第一天到最后一天的日期
````  
 select trunc(add_months(trunc(sysdate,'month'),-1)+rownum-1 from dual connect by rownum<= 
 (select to_number(to_char(last_day(add_months(sysdate,-1)),'dd'))from dual);
````

````yaml    


````