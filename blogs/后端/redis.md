---
title: Redis
date: 2024/11/29
tags:
  - Redis
categories:
  - 后端
---

# Redis 简介

Redis 是一个开源的键值对存储系统，通常被称为数据结构服务器。它支持多种数据结构，如字符串、哈希、列表、集合、有序集合等。Redis
以其高性能、低延迟和丰富的功能而著称，广泛应用于缓存、消息队列、会话存储等场景。

## 主要特性

- **高性能**：Redis 将数据存储在内存中，读写速度非常快。
- **持久化**：支持 RDB 和 AOF 两种持久化方式，确保数据不丢失。
- **数据结构丰富**：支持多种数据结构，满足不同应用场景的需求。
- **发布/订阅**：支持发布/订阅模式，适用于消息传递。
- **事务支持**：支持简单的事务处理。
- **主从复制**：支持主从复制，提高可用性和扩展性。

## 基本操作

### 启动 Redis 服务

```shell
redis-server /path/to/redis.conf
```

### 进入 Redis 命令行

```shell
redis-cli
```

### 常用命令

- **字符串操作**
    - 设置键值对：`SET key value`
    - 获取键值：`GET key`
    - 删除键：`DEL key`

- **哈希操作**
    - 设置哈希字段：`HSET key field value`
    - 获取哈希字段：`HGET key field`
    - 获取所有字段和值：`HGETALL key`

- **列表操作**
    - 从左侧插入元素：`LPUSH key value`
    - 从右侧插入元素：`RPUSH key value`
    - 从左侧弹出元素：`LPOP key`
    - 从右侧弹出元素：`RPOP key`

- **集合操作**
    - 添加元素：`SADD key member`
    - 检查成员是否存在：`SISMEMBER key member`
    - 获取所有成员：`SMEMBERS key`

- **有序集合操作**
    - 添加元素：`ZADD key score member`
    - 获取指定范围的成员：`ZRANGE key start stop`
    - 获取指定分数范围的成员：`ZRANGEBYSCORE key min max`

## 在SpringBoot使用redis的基本语法

### 1.导入依赖

```xml

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### 2.创建redis配置类

```java
package com.sky.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
@Slf4j
public class RedisConfiguration {

    @Bean
    public RedisTemplate redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        log.info("开始创建redis模版对象...");
        RedisTemplate redisTemplate = new RedisTemplate();
        //设置redis的连接工厂对象
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        //设置redis key的序列化器
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        return redisTemplate;
    }
}
```

### 3.通过RedisTemplate对象，代替原生操作

```java
package com.zjjhy.springboot;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.connection.DataType;
import org.springframework.data.redis.core.*;

import java.util.List;
import java.util.Set;
import java.util.concurrent.TimeUnit;

/**
 * Spring Data Redis 测试类
 */
@SpringBootTest
public class SpringDataRedisTest {

    @Autowired
    private RedisTemplate redisTemplate;

    /**
     * 测试 RedisTemplate 的基本操作
     */
    /**
     * 1.字符串（String）
     * 数据结构：字符串是最简单的数据类型，可以存储字符串、数字等。
     * <p>
     * 2.哈希（Hash）
     * 数据结构：哈希是一个键值对的集合，类似于 Java 中的 Map。
     * <p>
     * 3.列表（List）
     * 数据结构：列表是一个有序的字符串集合，可以进行从头部或尾部插入和删除操作。
     * <p>
     * 4.集合（Set）
     * 数据结构：集合是一个无序且不重复的字符串集合。
     * <p>
     * 5.有序集合（Sorted Set）
     * 数据结构：有序集合是一个有序的字符串集合，每个成员都有一个分数（score），成员按照分数排序。
     */
    @Test
    public void testRedisTemplate() {
        System.out.println(redisTemplate); // 打印 RedisTemplate 对象
        ValueOperations valueOperations = redisTemplate.opsForValue(); // 获取操作字符串类型数据的对象
        HashOperations hashOperations = redisTemplate.opsForHash(); // 获取操作哈希类型数据的对象
        ListOperations listOperations = redisTemplate.opsForList(); // 获取操作列表类型数据的对象
        SetOperations setOperations = redisTemplate.opsForSet(); // 获取操作集合类型数据的对象
        ZSetOperations zSetOperations = redisTemplate.opsForZSet(); // 获取操作有序集合类型数据的对象
    }

    /**
     * 操作字符串类型的数据
     */
    @Test
    public void testString() {
        // 设置键 "name" 的值为 "小明"
        redisTemplate.opsForValue().set("name", "小明");
        // 获取键 "name" 的值并打印
        String city = (String) redisTemplate.opsForValue().get("name");
        System.out.println(city);
        // 设置键 "code" 的值为 "1234"，并设置过期时间为 3 分钟
        redisTemplate.opsForValue().set("code", "1234", 3, TimeUnit.MINUTES);
        // 如果键 "lock" 不存在，则设置其值为 "1"
        redisTemplate.opsForValue().setIfAbsent("lock", "1");
        // 尝试设置键 "lock" 的值为 "2"，但由于键已存在，不会生效
        redisTemplate.opsForValue().setIfAbsent("lock", "2");
    }

    /**
     * 操作哈希类型的数据
     */
    @Test
    public void testHash() {
        HashOperations hashOperations = redisTemplate.opsForHash(); // 获取操作哈希类型数据的对象

        // 在哈希 "100" 中设置键 "name" 的值为 "tom"
        hashOperations.put("100", "name", "tom");
        // 在哈希 "100" 中设置键 "age" 的值为 "20"
        hashOperations.put("100", "age", "20");

        // 获取哈希 "100" 中键 "name" 的值并打印
        String name = (String) hashOperations.get("100", "name");
        System.out.println(name);

        // 获取哈希 "100" 的所有键并打印
        Set keys = hashOperations.keys("100");
        System.out.println(keys);

        // 获取哈希 "100" 的所有值并打印
        List values = hashOperations.values("100");
        System.out.println(values);

        // 删除哈希 "100" 中的键 "age"
        hashOperations.delete("100", "age");
    }

    /**
     * 操作列表类型的数据
     */
    @Test
    public void testList() {
        ListOperations listOperations = redisTemplate.opsForList(); // 获取操作列表类型数据的对象

        // 向列表 "mylist" 左侧批量插入元素 "a", "b", "c"
        listOperations.leftPushAll("mylist", "a", "b", "c");
        // 向列表 "mylist" 左侧插入元素 "d"
        listOperations.leftPush("mylist", "d");

        // 获取列表 "mylist" 的所有元素并打印
        List mylist = listOperations.range("mylist", 0, -1);
        System.out.println(mylist);

        // 从列表 "mylist" 右侧弹出一个元素
        listOperations.rightPop("mylist");

        // 获取列表 "mylist" 的长度并打印
        Long size = listOperations.size("mylist");
        System.out.println(size);
    }

    /**
     * 操作集合类型的数据
     */
    @Test
    public void testSet() {
        SetOperations setOperations = redisTemplate.opsForSet(); // 获取操作集合类型数据的对象

        // 向集合 "set1" 添加元素 "a", "b", "c", "d"
        setOperations.add("set1", "a", "b", "c", "d");
        // 向集合 "set2" 添加元素 "a", "b", "x", "y"
        setOperations.add("set2", "a", "b", "x", "y");

        // 获取集合 "set1" 的所有成员并打印
        Set members = setOperations.members("set1");
        System.out.println(members);

        // 获取集合 "set1" 的大小并打印
        Long size = setOperations.size("set1");
        System.out.println(size);

        // 获取集合 "set1" 和 "set2" 的交集并打印
        Set intersect = setOperations.intersect("set1", "set2");
        System.out.println(intersect);

        // 获取集合 "set1" 和 "set2" 的并集并打印
        Set union = setOperations.union("set1", "set2");
        System.out.println(union);

        // 从集合 "set1" 中删除元素 "a", "b"
        setOperations.remove("set1", "a", "b");
    }

    /**
     * 操作有序集合类型的数据
     */
    @Test
    public void testZset() {
        ZSetOperations zSetOperations = redisTemplate.opsForZSet(); // 获取操作有序集合类型数据的对象

        // 向有序集合 "zset1" 添加元素 "a"，分数为 10
        zSetOperations.add("zset1", "a", 10);
        // 向有序集合 "zset1" 添加元素 "b"，分数为 12
        zSetOperations.add("zset1", "b", 12);
        // 向有序集合 "zset1" 添加元素 "c"，分数为 9
        zSetOperations.add("zset1", "c", 9);

        // 获取有序集合 "zset1" 的所有元素并打印
        Set zset1 = zSetOperations.range("zset1", 0, -1);
        System.out.println(zset1);

        // 增加有序集合 "zset1" 中元素 "c" 的分数 10
        zSetOperations.incrementScore("zset1", "c", 10);

        // 从有序集合 "zset1" 中删除元素 "a", "b"
        zSetOperations.remove("zset1", "a", "b");
    }

    /**
     * 通用命令操作
     */
    @Test
    public void testCommon() {
        // 获取所有键并打印
        Set keys = redisTemplate.keys("*");
        System.out.println(keys);

        // 检查键 "name" 是否存在
        Boolean name = redisTemplate.hasKey("name");
        // 检查键 "set1" 是否存在
        Boolean set1 = redisTemplate.hasKey("set1");

        // 遍历所有键，获取每个键的数据类型并打印
        for (Object key : keys) {
            DataType type = redisTemplate.type(key);
            System.out.println(type.name());
        }

        // 删除键 "mylist"
        redisTemplate.delete("mylist");
    }
}
```

## 在springboot中使用redis完成前台热点数据分页与模糊查询案例

```java
package com.zjjhy.service.impl;

import cn.hutool.core.util.ObjectUtil;
import com.zjjhy.common.enums.ResultCodeEnum;
import com.zjjhy.common.exception.BusinessException;
import com.zjjhy.common.interface_constants.Constants;
import com.zjjhy.common.pojo.dto.DocsDto;
import com.zjjhy.common.pojo.entity.Docs;
import com.zjjhy.common.pojo.vo.DocsVo;
import com.zjjhy.common.pojo.vo.PageVo;
import com.zjjhy.mapper.AdminSystemHomeMapper;
import com.zjjhy.service.FrontService;
import org.springframework.beans.BeanUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.ListOperations;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;

@Service
public class FrontServiceImpl implements FrontService {

    @Autowired
    private AdminSystemHomeMapper adminSystemHomeMapper;

    @Autowired
    private RedisTemplate<String, DocsVo> redisTemplate;

    /**
     * 前台用户获取数据
     *
     * @param docsDto
     * @param page
     * @param pageSize
     * @return
     */
    @Override
    public PageVo<DocsVo> getDocs(DocsDto docsDto, Integer page, Integer pageSize) {
        //分页返回结果
        PageVo<DocsVo> pageVo = new PageVo<>();

        //redis存储文章列表的键
        String redisKey = Constants.REDIS_KEY;

        //获取操作redis的列表数据对象
        ListOperations<String, DocsVo> list = redisTemplate.opsForList();

        //使用range拿到所有范围数据(能取到结束索引的值，不是左闭右开)
        List<DocsVo> range = list.range(redisKey, 0, -1);

        //1.如果从redis查出来的数据不是空,则处理后直接返回redis中的数据
        if (ObjectUtil.isNotEmpty(range)) {
            int beginPage = (page - 1) * pageSize;

            int endPage = Math.min((beginPage + pageSize), range.size());

            //按照id排序避免顺序混乱
            range.sort(Comparator.comparingInt(DocsVo::getId));

            try {
                //如果形参对象的文档标题是空不用判断直接把redis返回的数据返回
                if (ObjectUtil.isEmpty(docsDto.getDocsTitle())) {
                    pageVo.setData(range.subList(beginPage, endPage));//subList()左闭右开
                    pageVo.setTotal(range.size());
                } else {
                    //如果形参对象的文档标题不是空直接需要用contains方法返回符合条件的数据
                    List<DocsVo> list1 = new ArrayList<>();
                    for (DocsVo next : range) {
                        if (next.getDocsTitle().contains(docsDto.getDocsTitle())) {
                            list1.add(next);
                        }
                    }
                    //过滤过的结束索引需重新计算
                    int endPage2 = Math.min((beginPage + pageSize), list1.size());
                    //把处理好的数据赋值给pageVo准备响应前端
                    pageVo.setData(list1.subList(beginPage, endPage2));
                    pageVo.setTotal(list1.size());
                }

                return pageVo;
            } catch (NullPointerException e) {
                //抛出业务异常传递一个Throwable对象作为其他原因
                throw new BusinessException(ResultCodeEnum.SYSTEM_ERROR, e);
            }
        }

        //2.redis没有数据，调用mapper层查询返回
        Docs docs = new Docs();
        BeanUtils.copyProperties(docsDto, docs);
        pageVo.setTotal(adminSystemHomeMapper.queryByDocsTotal(docs));
        pageVo.setData(adminSystemHomeMapper.queryByDocsData(docs, (page - 1) * pageSize, pageSize));
        //将查询到的数据批量插入redis，后续就不用调用mysql查询
        list.leftPushAll(redisKey, adminSystemHomeMapper.queryDocsData());

        return pageVo;
    }
}
```

## 总结

Redis 是一个高性能、功能丰富的键值对存储系统，适用于多种应用场景。通过合理配置和使用，可以充分发挥其性能优势。
