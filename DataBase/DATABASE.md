---
layout: default
title: DATABASE
nav_order: 4
has_children: true
permalink: docs/Database
---

## 数据库
{: .no_toc }

### 数据库优化
#### 对表分区
- 分区表是指将一个大表拆分成多个小表，每个小表称为一个分区，分区表中包含多个分区，每个分区对应一个独立的物理存储空间。
- partition的用法
    
#### 添加索引

#### 数据库无缝切换
- [数据库方言-数据库切换](http://www.manongjc.com/detail/64-vitbczditlzxdos.html)
#### druid-*.*.*.jar含有加密解密功能，提供连接池的功能

#### 使用注解注入的方法配置DataSource，并使用properties配置数据库信息，运用druid加密解密
````
- 1.properties文件（以oracle为例子）：
jdbc.driverName = oracle.jdbc.driver.OracleDriver
jdbc.url=jdbc:oracle:thin:@192.168.10.118:1521:orcl
jdbc.username=bvis
jdbc.password=VqDpinLB4YpisgZKYl9CjDE92WzQCrizwKyoLJnyvcdqrwZ8v4/SCk+5qJuCohTIuiGA7m36RNPavcPqG+E49A==
jdbc.publicKey=MFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBAOVH+pKnFycnmbXW0PvO4R6BuAzAcreawzHunWkNwzdJJfqVwMbV5jc4ZgNs8ehZk2eAYzNqdAOrsEHd6yuMUUMCAwEAAQ==
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
