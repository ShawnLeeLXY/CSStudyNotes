# Redis

## 第1节 Redis入门

### 1 Redis概述

Redis是用C语言开发的一款高性能的NoSQL系列的非关系型数据库

- 关系型数据库：
  1. 数据之间由关联关系
  2. 数据存储在硬盘的文件上
- 非关系型数据库：
  1. 数据之间没有关联关系
  2. 数据存储在内存中



### 2 下载与安装

[Redis官网](https://redis.io/)

[Redis中文网](http://www.redis.cn/)

以上都可以下载

下载--->解压--->开启服务器端（windows上是redis-server.exe）--->开启客户端（windows上是redis-cli.exe）



### 3 命令操作

redis的数据结构：key+value

key都是字符串

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

- 存储：hset key filed value
- 获取：
  - hget key filed value 获取指定的filed对应的值
  - hgetall key 获取所有的filed和value
- 删除：hdel key field

列表类型list：双端队列

- 添加：
  - lpush key value 左边加入
  - rpush key value 右边加入
- 获取：lrange key start end：范围获取
- 删除：
  - lpop key：删除列表最左边的元素，并将元素返回
  - rpop key：删除列表最右边的元素，并将元素返回

集合类型set：不允许重复元素

1. 存储：sadd key value
2. 获取：smembers key 获取set集合中的所有元素
3. 删除 ：srem key value 删除set集合中的某个元素

有序集合类型sorted set：不允许元素重复，且元素有序

1. 存储：zadd key score value
2. 获取：zrange key start end
3. 删除：zrem key value



### 4 通用命令

keys * 查询所有的键

type key 获取键对应的value的类型

del key 删除指定的key value



### 5 持久化

Redis是一个内存数据库，可以将Redis内存中的数据持久化保存到硬盘的文件中

Redis持久化机制：

1. RDB：在一定的间隔时间中，检测key的变化情况，然后持久化数据。

   *默认方式，不需要进行配置，默认就使用这种机制*

2. AOF：每一次命令操作后即持久化数据。

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

