---
title: Redis基础
date: 2024-09-14 13:27:50
excerpt: 本文对于Redis数据库的基本用法做了系统性总结，持续更新
tags:
  - 数据库
categories:
  - [计算机基础,数据库]
---

# 1. Redis简介

Redis 是一个开源的内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件。

Redis是用C语言开发的一个开源的高性能键值对（key-value）数据库，官方提供的数据是可以达到 100000+ 的 QPS （每秒内查询次数）。它存储的 value 类型比较丰富，也被称为结构化的 NoSql 数据库。

> NoSql (Not Only SQL)，不仅仅是SQL，泛指**非关系型数据库**，NoSql数据库并不是要取代关系型数据库，而是关系型数据库的补充。

# 2. Redis数据类型

Redis 存储的是 key-value 结构的数据，其中 key 是字符串类型，value 有 5 种常用的数据类型👇

> 字符串string，哈希hash，列表list，集合set，有序集合sorted set

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202310162110822.png" alt="image-20231016211035639" style="zoom: 67%;" />

# 3. Redis常用命令

## 3.1 string常用命令

```python
# 设置指定key的值
set key value
# 获取指定key的值
get key
# 设置指定key的值，并将key的过期时间设为seconds秒
setex key seconds value
# 只有再key不存在时设置key的值
setnx key value
```

## 3.2 hash常用命令

Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象，常用命令👇

```python
# 将哈希表 key 中的字段 field 的值设为 value
hset key field value
# 获取存储再哈希表中指定字段的值
hget key field
# 删除存储在哈希表中的指定字段
hdel key field 
# 获取哈希表中的所有字段
hkeys key
# 获取哈希表中所有值
hvals key
# 获取在哈希表中指定 key 的所有字段和值
hgetall key 
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202310162142732.png" alt="image-20231016214206697" style="zoom: 50%;" />

## 3.3 list 常用命令

Redis 列表是简单的**字符串列表**，按照插入顺序排序，常用命令👇

```python
# 将一个或多个值插入到列表头部
lpush key value1 [value2]
# 获取列表指定索引范围内的元素
lrange key start stop
# 移除并获取列表最后一个元素
rpop key
# 获取列表长度
llen key
# 移出并获取列表的最后一个元素，如果列表没有元素回阻塞列表直到等待超时或发现可弹出元素为止
brpop key1 [key2] timeout
```

## 3.4 set常用命令

set 是 string 类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据，常用命令👇

```python
# 向集合添加一个或多个成员
sadd key member1 [member2]
# 返回集合中的所有成员
smembers key 
# 获取集合的成员数
scard key
# 返回给定所有集合的交集
sinter key1 [key2]
# 返回所有给定集合的并集
sunion key1 [key2]
# 返回给定所有集合的差集
sdiff key1 [key2]
# 移除集合中一个或多个成员
srem key member1 [member2]
```

## 3.5 sorted set常用命令

sorted set 有序集合是 string 类型元素的集合，且不允许重复的成员。每个元素都会关联一个 double 类型的分数 score，redis 正是通过分数来为集合中的成员进行从小到大排序。有序集合的成员是唯一的，但分数却可以重复。

```python
# 向有序集合添加一个或多个成员，或者更新已存在成员的分数
zadd key score1 member1 [score2 member2]
# 通过索引区间返回有序集合中指定区间内的成员
zrange key start stop [withscores]
# 有序集合中对指定成员的分数加上增量increment
zincrby key increment member
# 移除有序集合中的一个或者多个成员
zrem key member [member...]
```

## 3.6 通用命令

```python
# 查找所有符合给定模式pattern的key
keys pattern
# 检查给定key是否存在
exists key
# 返回key所存储的值的类型
type key
# 返回给定key的剩余生存时间TTL，以秒为单位
ttl key
# 该命令用于在key存在是删除key
del key
```

# 4. Java使用Redis

通常项目是基于 Spring Boot 开发，因此使用 Spring Dara Redis 操作 Redis

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <version>3.1.0</version>
</dependency>
```

Spring Data Redis 中提供了一个高度封装的类：RedisTemplate，针对 jedis 客户端中大量 api 进行了归类封装，将同一类型操作封装为 operation 接口，具体分类如下👇

+ ValueOperations：简单 K-V 操作
+ SetOperations：set 类型数据操作
+ ZSetOperations：zset 类型数据操作
+ Hash0perations：针对 map 类型的数据操作
+ ListOperations：针对 list 类型的数据操作

通过自动装配的RedisTemplate类的opsForxxx方法获得上述对象👆，从而完成对 Redis 的操作，使用前需要手动启动 Redis 服务器

```java
// classes传入主应用程序类
@SpringBootTest(classes = ReggieApplication.class)
// 在测试中加载Spring的上下文
@RunWith(SpringRunner.class)
public class SpringDaraRedisTest {
    @Autowired
    private RedisTemplate redisTemplate;
    @Test
    public void testString(){
        // 对应string常用命令的set key value 方法
        redisTemplate.opsForValue().set("city","GZ");
    }
}
```

需要手动编写配置类以保证在 Spring Boot 中操作 key 值后，传入 Redis 时依旧为 string 类型，value 不做序列化为字符串的操作，否则会导致存入的value值只能为 string。

```java
@Configuration
public class RedisConfig extends CachingConfigurerSupport {
    @Bean
    public RedisTemplate<Object,Object> redisTemplate(RedisConnectionFactory connectionFactory){
        RedisTemplate<Object,Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        // 默认的key序列化器为JdkSerializationRedisSerializer
        redisTemplate.setConnectionFactory(connectionFactory);
        return redisTemplate;
    }
}
```
