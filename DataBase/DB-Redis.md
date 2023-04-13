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
  不同的是每个元素都会关联一个double类型的分数。redis正是通过分数（分数是自己设定的）来为集合中的成员进行从小到大的排序。
  zset的成员是唯一的,但分数(score)却可以重复。
  
### 命令 具体可看菜鸟教程

## springboot整合redis   在SpringBoot中一般使用RedisTemplate提供的方法来操作Redis。
- [springboot整合redis讲解地址](https://blog.csdn.net/qq_36781505/article/details/86612988)
- 1.引入依赖：spring-boot-starter-data-redis
- 2.在yml中配置相关的redis配置：
````yaml
spring: 
    redis:
#        database: 0  # redis在启动时会默认有16个database供使用，配置为0则默认数据存储在db[0]中
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
在JAVA中，一切皆对象，而将对象的状态信息转为存储或传输的形式需要序列化。 详情可看 (../java/Serialization.md)

## RedisTemplate 相关方法
- [RedisTemplate方法说明网址](https://blog.csdn.net/m0_49790240/article/details/122338413)

## 本地下载redis安装包后可以在桌面放置脚本启动redis/redis-cli
````
redis-cli:
    cmd /k "cd /d  D:\somesoft\Redis-x64-3.0.504&&redis-cli.exe  -h 127.0.0.1 -p 6379"
redis启动:
    cmd /k "cd /d  D:\somesoft\Redis-x64-3.0.504&&redis-server.exe   redis.windows.conf"

````
- 注意：当设置序列化器的时候value使用的序列化器为new JdkSerializationRedisSerializer()，
    则在客户端通过key值查找value（baiBai）时可能出现前缀：\xac\xed\x00\x05t\x00\x06baiBai，
    去除前缀可以将序列化器换成new Jackson2JsonRedisSerializer<>(Object.class)

## redis发布订阅： (pub/sub) 是一种消息通信模式：发送者 (pub) 发送消息，订阅者 (sub) 接收消息。
### 步骤：启用两个客户端
- 第一个客户端：订阅名为qqWy的频道 --->  subscribe qqWy
- 第二个客户端： 往qqWy的频道发送消息 ---> publish qqWy "xiao shi hou zhen ke ai"
- 发送完成订阅者的客户端（第一个）会显示以下消息
````
1) "message"
2) "qqWy"
3) "xiao shi hou zhen ke ai"
````
## redis 事务
- redis事务可以一次执行多个命令，需要注意的是：
````
批量操作在发送 EXEC 命令前被放入队列缓存。
收到 EXEC 命令后进入事务执行，事务中任意命令执行失败，其余的命令依然被执行。
在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中。
````
- 一个事务从开始到执行会经历以下三个阶段：
````
  开始事务。
  命令入队。
  执行事务。
````
- 示例：
````
127.0.0.1:6379> multi #标记一个事务的开始
OK
127.0.0.1:6379> set qq 21
QUEUED
127.0.0.1:6379> get qq
QUEUED
127.0.0.1:6379> sadd xiaoming bb
QUEUED
127.0.0.1:6379> smembers xiaoming
QUEUED
127.0.0.1:6379> exec  #执行所有事务块内的命令
1) OK
2) "21"
3) (integer) 1
4) 1) "bb"
````
- 单个 Redis 命令的执行是原子性的，但 Redis 没有在事务上增加任何维持原子性的机制，所以 Redis 事务的执行并不是原子性的。
  事务可以理解为一个打包的批量执行脚本，但批量指令并非原子化的操作，中间某条指令的失败不会导致前面已做指令的回滚，也不会造成后续的指令不做。