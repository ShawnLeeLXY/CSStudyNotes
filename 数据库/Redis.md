# Redis

## 第1节 Redis入门

### 1 Redis概述

Redis是用C语言开发的一款高性能的基于键值对的NoSQL系列的非关系型数据库

- 关系型数据库：
  1. 数据之间由关联关系
  2. 数据存储在硬盘的文件上
- 非关系型数据库（NoSQL）：
  1. 数据之间没有关联关系
  2. 数据存储在内存中

Redis将所有的数据都存放在**内存**中，所以读写性能非常高

Redis可以将内存中的数据以**快照**或**日志**的形式保存到硬盘上（持久化）

应用场景：缓存、排行榜、计数器、社交网络、消息队列等





### 2 下载与安装

[Redis官网](https://redis.io/)

[Redis中文网](http://www.redis.cn/)

以上都可以下载

下载--->解压--->开启服务器端（windows上是redis-server.exe）--->开启客户端（windows上是redis-cli.exe）





### 3 命令操作

Redis默认有16个库（索引0~15）

`select 1`选择索引为1的库

`flushdb`刷新库：清除该库里的所有数据



Redis的数据结构：key+value

key都是字符串

key可以为null

`incr key`将key对应的value值加1然后返回

`decr key`将key对应的value值减1然后返回

value的数据结构：

1. 字符串string
2. 哈希hash
3. 列表list
4. 集合set
5. 有序集合sortedset



字符串类型string：

- 存储：set key value
- 获取：get key
- 删除：del key



哈希类型hash：

- 存储：hset key field value
- 获取：
  - hget key feild value 获取指定的field对应的值
  - hgetall key 获取所有的field和value
- 删除：hdel key field



列表类型list：双端队列

- 添加：
  - lpush [key] [value] 左边加入
  - rpush [key] [value] 右边加入
- 获取：
  - lindex [key] [index]：索引获取
  - lrange [key] [start] [end]：范围获取
- 删除：
  - lpop key：删除列表最左边的元素，并将元素返回
  - rpop key：删除列表最右边的元素，并将元素返回



集合类型set：不允许重复元素

1. 存储：sadd key value
2. 获取：smembers key 获取set集合中的所有元素
3. 删除 ：srem key value 删除set集合中的某个元素



有序集合类型sorted set：不允许元素重复，且元素有序

sorted set给每个元素添加了score，按照score进行排序

1. 存储：zadd key score value
2. 获取：zrange key start end
3. 删除：zrem key value





### 4 通用命令

`keys *` 查询所有的键

`type key` 获取键对应的value的类型

`del key` 删除指定的key value





### 5 持久化

Redis是一个内存数据库，可以将Redis内存中的数据持久化保存到硬盘的文件中

Redis持久化机制：

1. RDB：在一定的间隔时间中，检测key的变化情况，然后持久化数据

   *快照的形式，不需要进行配置，默认就使用这种机制*

2. AOF：每一次命令操作后即持久化数据（增量存）

   *日志记录的方式，可以记录每一条命令的操作*

使用步骤：

1. 修改配置文件redis.windows.conf
2. 使用命令行重新启动redis服务器，并指定配置文件名称







## 第2节 Jedis

### 1 Jedis

Jedis：一款Java操作Redis数据库的工具

搭建Maven后，在pom.xml引入依赖：

```xml
<!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.9.0</version>
        </dependency>
```



Jedis操作数据结构代码演示：

```java
package com.lxy.jedis;

import org.junit.jupiter.api.Test;
import redis.clients.jedis.Jedis;

import java.util.List;
import java.util.Map;
import java.util.Set;

public class JedisTest {
    @Test
    public void test1() {
        // 获取连接
        Jedis jedis = new Jedis("localhost", 6379);
        // 操作
        jedis.set("username", "Alice");
        // 关闭连接
        jedis.close();
    }

    @Test
    public void test2() {
        // 获取连接
        // 空参构造默认值"localhost" 6379端口
        //Jedis jedis = new Jedis();
        Jedis jedis = new Jedis();
        // 操作
        jedis.set("username", "Alice");
        // 获取string
        String username = jedis.get("username");
        System.out.println(username);
        // setx()设置过期时间
        // 将activecode: hehe键值对存入redis，20秒后自动删除该键值对
        jedis.setex("activecode", 20, "hehe");
        // 关闭连接
        jedis.close();
    }

    @Test
    public void test3() {
        // 获取连接
        Jedis jedis = new Jedis();
        // 操作set
        jedis.hset("myhash", "name", "Alice");
        jedis.hset("myhash", "age", "21");
        jedis.hset("myhash", "gender", "female");
        // 获取hash
        String name = jedis.hget("myhash", "name");
        System.out.println(name);
        // 获取hash的所有map中的数据
        Map<String, String> map = jedis.hgetAll("myhash");
        Set<String> keySet = map.keySet();
        for (String key : keySet) {
            String value = map.get(key);
            System.out.println(key + ": " + value);
        }
        // 关闭连接
        jedis.close();
    }

    @Test
    public void test4() {
        // 获取连接
        Jedis jedis = new Jedis();
        // 操作list
        jedis.lpush("mylist", "a", "b", "c");
        jedis.rpush("mylist", "a", "b", "c");
        List<String> list = jedis.lrange("mylist", 0, -1);
        System.out.println(list);
        String element1 = jedis.lpop("mylist");
        String element2 = jedis.rpop("mylist");
        System.out.println(element1 + ", " + element2);
        List<String> listNew = jedis.lrange("mylist", 0, -1);
        System.out.println(listNew);
        // 关闭连接
        jedis.close();
    }

    @Test
    public void test5() {
        // 获取连接
        Jedis jedis = new Jedis();
        // 操作set
        jedis.sadd("myset", "c", "c++", "java");
        Set<String> set = jedis.smembers("myset");
        System.out.println(set);
        // 关闭连接
        jedis.close();
    }

    @Test
    public void test6() {
        // 获取连接
        Jedis jedis = new Jedis();
        // 操作sorted set
        jedis.zadd("mysortedset", 90, "Alice");
        jedis.zadd("mysortedset", 80, "Betty");
        jedis.zadd("mysortedset", 95, "Cindy");
        Set<String> sortedset = jedis.zrange("mysortedset", 0, -1);
        System.out.println(sortedset);
        // 关闭连接
        jedis.close();
    }
}
```





### 2 Jedis连接池

使用步骤：

1. 创建JedisPool连接池对象
2. 调用`getResource()`方法获取Jedis连接

Jedis连接池代码演示：

JedisPoolUtils.java

```java
package com.lxy.util;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

public class JedisPoolUtils {
    private static JedisPool jedisPool;

    static {
        InputStream is = JedisPoolUtils.class.getClassLoader().getResourceAsStream("jedis.properties");
        Properties pro = new Properties();
        try {
            pro.load(is);
        } catch (IOException e) {
            e.printStackTrace();
        }
        JedisPoolConfig config = new JedisPoolConfig();
        config.setMaxTotal(Integer.parseInt(pro.getProperty("maxTotal")));
        config.setMaxIdle(Integer.parseInt(pro.getProperty("maxIdle")));
        jedisPool = new JedisPool(config, pro.getProperty("host"), Integer.parseInt(pro.getProperty("port")));
    }

    public static Jedis getJedis() {
        return jedisPool.getResource();
    }
}
```

JedisPoolTest.java

```java
package com.lxy.jedis;

import com.lxy.util.JedisPoolUtils;
import org.junit.jupiter.api.Test;
import redis.clients.jedis.Jedis;

public class JedisPoolTest {
    @Test
    public void test() {
        // 创建配置对象
        Jedis jedis = JedisPoolUtils.getJedis();
        // 使用
        jedis.set("username", "Alice");
        // 归还到连接池
        jedis.close();
    }
}
```







## 第3节 Spring Boot整合Redis

### 1 Spring Boot整合Redis的步骤

**引入依赖：**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <version>2.5.2</version>
</dependency>
```



**Spring-data-redis**是spring大家族的一部分，提供了在srping应用中通过简单的配置访问redis服务，对reids底层开发包(Jedis, JRedis, and RJC)进行了高度封装，**RedisTemplate**提供了redis各种操作、异常处理及序列化，支持发布订阅，并对spring 3.1 cache进行了实现。



**配置Redis：**

- 配置数据库参数（默认key类型是Object）
- 编写配置类，构造RedisTemplate（建议修改key类型为String）



修改application.properties：

```properties
# RedisProperties
# 使用索引为10的数据库
spring.redis.database=10
# 数据库的本机ip
spring.redis.host=localhost
# 端口号 默认是6379
spring.redis.port=6379
```



编写配置类RedisConfig：

```java
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(factory);

        // 设置key的序列化方式
        template.setKeySerializer(RedisSerializer.string());
        // 设置value的序列化方式（序列化为json）
        template.setValueSerializer(RedisSerializer.json());
        // 设置hash的key的序列化方式
        template.setHashKeySerializer(RedisSerializer.string());
        // 设置hash的value的序列化方式
        template.setHashValueSerializer(RedisSerializer.json());
        // 设置完后触发生效
        template.afterPropertiesSet();

        return template;
    }

}
```



**访问Redis：**

- 字符串string：`redisTemplate.opsForValue()`
- 哈希hash：`redisTemplate.opsForHash()`
- 列表list：`redisTemplate.opsForList()`
- 集合set：`redisTemplate.opsForSet()`
- 有序集合sorted set：`redisTemplate.opsForZSet()`



编写测试类RedisTest：

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@ContextConfiguration(classes = RedisDemoApplication.class)
public class RedisTests {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    public void testStrings() {
        String redisKey = "test:count";
        redisTemplate.opsForValue().set(redisKey, 1);
        System.out.println(redisTemplate.opsForValue().get(redisKey));
        System.out.println(redisTemplate.opsForValue().increment(redisKey));
        System.out.println(redisTemplate.opsForValue().decrement(redisKey));
    }

    @Test
    public void testHashes() {
        String redisKey = "test:user";
        redisTemplate.opsForHash().put(redisKey, "id", 1);
        redisTemplate.opsForHash().put(redisKey, "username", "zhangsan");
        System.out.println(redisTemplate.opsForHash().get(redisKey, "id"));
        System.out.println(redisTemplate.opsForHash().get(redisKey, "username"));
    }

    @Test
    public void testLists() {
        String redisKey = "test:ids";
        redisTemplate.opsForList().leftPush(redisKey, 101);
        redisTemplate.opsForList().leftPush(redisKey, 102);
        redisTemplate.opsForList().leftPush(redisKey, 103);
        System.out.println(redisTemplate.opsForList().size(redisKey));
        System.out.println(redisTemplate.opsForList().index(redisKey, 0));
        System.out.println(redisTemplate.opsForList().range(redisKey, 0, 2));
        System.out.println(redisTemplate.opsForList().leftPop(redisKey));
        System.out.println(redisTemplate.opsForList().leftPop(redisKey));
        System.out.println(redisTemplate.opsForList().leftPop(redisKey));
    }

    @Test
    public void testSets() {
        String redisKey = "test:teachers";
        redisTemplate.opsForSet().add(redisKey, "Alice", "Betty", "Cindy", "Dianna", "Emily");
        System.out.println(redisTemplate.opsForSet().size(redisKey));
        // 随机弹出一个值
        System.out.println(redisTemplate.opsForSet().pop(redisKey));
        System.out.println(redisTemplate.opsForSet().members(redisKey));
    }

    @Test
    public void testSortedSets() {
        String redisKey = "test:students";
        redisTemplate.opsForZSet().add(redisKey, "Flora", 80);
        redisTemplate.opsForZSet().add(redisKey, "Grace", 90);
        redisTemplate.opsForZSet().add(redisKey, "Hedy", 50);
        redisTemplate.opsForZSet().add(redisKey, "Isabel", 70);
        redisTemplate.opsForZSet().add(redisKey, "Jean", 60);
        System.out.println(redisTemplate.opsForZSet().zCard(redisKey));
        System.out.println(redisTemplate.opsForZSet().score(redisKey, "Hedy"));
        System.out.println(redisTemplate.opsForZSet().reverseRank(redisKey, "Hedy"));
        System.out.println(redisTemplate.opsForZSet().reverseRange(redisKey, 0, 2));
    }

    @Test
    public void testKeys() {
        redisTemplate.delete("test:user");
        System.out.println(redisTemplate.hasKey("test:user"));
        redisTemplate.expire("test:students", 10, TimeUnit.SECONDS);
    }

    // 多次访问同一个key 绑定key来简化代码
    @Test
    public void testBoundOperations() {
        String redisKey = "test:count";
        BoundValueOperations operations = redisTemplate.boundValueOps(redisKey);
        operations.increment();
        operations.increment();
        operations.increment();
        operations.increment();
        operations.increment();
        System.out.println(operations.get());
    }
    
}
```



右键运行测试方法`testStrings()`，控制台打印输出：

![](Redis.assets/redisTest.png)





### 2 Redis的事务管理

> 事务是数据库管理系统执行过程中的一个逻辑单位，由一个有限的数据库操作序列构成。

Redis事务的三个状态：

1. 开始：以MULTI开始一个事务
2. 入队：将多个命令入队到事务中，接到这些命令并不会立即执行，而是放到等待执行的事务队列里面
3. 执行：由EXEC命令触发事务

Redis不支持事务回滚

查询命令应放在事务之前或之后

```java
// 编程式事务
@Test
public void testTransactional() {
    Object obj = redisTemplate.execute(new SessionCallback() {
        @Override
        public Object execute(RedisOperations redisOperations) throws DataAccessException {
            String redisKey = "redis:tx";
            redisOperations.multi();
            redisOperations.opsForSet().add(redisKey, "Alice");
            redisOperations.opsForSet().add(redisKey, "Betty");
            redisOperations.opsForSet().add(redisKey, "Cindy");
            // 事务内部做查询是无效的
            System.out.println(redisOperations.opsForSet().members(redisKey));
            return redisOperations.exec();
        }
    });
    System.out.println(obj);
}
```

右键运行测试方法，控制台打印输出：

![](Redis.assets/RedisTransactional.png)

