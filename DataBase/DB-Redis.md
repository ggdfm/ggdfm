---
layout: default
title: DB-Redis
nav_order: 3
parent: DATABASE
---

# Redis
{: .no_toc }

## redis 基本信息
### 数据类型
- string（字符串）
  string类型是二进制安全的，可以包含任意数据。比如jpg图片或者序列化的对象
- hash（哈希）
  hash是键值对集合
- list（列表）
  Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）
- set（集合）（集合内元素唯一性）
  Redis 的 Set 是 string 类型的无序集合。 
  集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。
- zset（sorted set：有序集合）查找是使用稀疏索引进行查找的[详情请看](https://www.cnblogs.com/wuyizuokan/p/11108417.html)
  Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
  不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。 
  zset的成员是唯一的,但分数(score)却可以重复。
  
### 命令 具体可看菜鸟教程

## springboot整合redis   在SpringBoot中一般使用RedisTemplate提供的方法来操作Redis。
- [springboot整合redis讲解地址](https://blog.csdn.net/qq_36781505/article/details/86612988)
- 1.引入依赖：spring-boot-starter-data-redis
- 2.在yml中配置相关的redis配置：
````yaml
spring: 
    redis:
#        database: 0
        host: 192.161.151.177
        password: Qq#51
        port: 5170
````
- 3.测试  注入RedisTemplate对象，调用方法即可。
- 4.序列化配置--在config.java中配置，使用@Bean注入。
````java
@Configuration
public class RedisConfig{
    @Bean
    public RedisTemplate<String,Object>redisTemplate(RedisConnectionFactory factory){
        RedisTemplate<String,Object>template=new RedisTemplate<>();
        //关联
        template.setConnectionFactory(factory);
        //设置key的序列化器
        template.setKeySerializer(new StringRedisSerializer());
        //设置value的序列化器
         template.setValueSerializer(new StringRedisSerializer());//或将new StringRedisSerializer()换成其他序列化器
        return template;
    }
}
````
在JAVA中，一切皆对象，而将对象的状态信息转为存储或传输的形式需要序列化。 详情可看 ![序列化与反序列化](../java/Serialization.md)

## RedisTemplate 相关方法
- [RedisTemplate方法说明网址](https://blog.csdn.net/m0_49790240/article/details/122338413)