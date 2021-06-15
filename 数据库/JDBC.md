# 第一部分 JDBC基础

## 第1节 JDBC简介

### 1 简介

JDBC（Java Data Base Connectivity）：Java数据库连接，是一种用于执行SQL语句的Java API

数据库驱动：通常是一个jar包

驱动：两个设备之间通信的桥梁



### 2 JDBC代码实现

步骤：

1. 创建数据库和表

2. 引入数据库驱动（jar包）

3. 获得连接

4. 编写SQL

5. 对于Statement：创建Statement对象；

   对于PreparedStatement：预编译SQL

6. 执行SQL

7. 释放资源





## 第2节 API介绍

### 1 DriverManager

DriverManager：注册驱动

方式一：`DriverManager.registerDriver(new Driver())`

方式二：`Class.forName(/*Driver包路径*/)`



Connection中url的写法：`jdbc:mysql://localhost:3306/xxx`

- jdbc是连接数据库的协议
- mysql是jdbc的子协议
- xxx是数据库名称

可缩写为`jdbc:mysql:///xxx`



### 2 Connection接口

子接口：

1. Statement接口：执行SQL语句
   - `boolean execute(String sql)`增删查改
   - `ResultSet executeQuery(String sql)`查询
   - `int executeUpdate(String sql)`增删改
2. CallableStatement接口存储对象
3. PreparedStatement接口
   - 解决SQL注入漏洞
   - 预编译SQL语句

commit()事务提交

rollback()事务回滚

ResultSet结果集

结果集遍历原理：使用`next()`方法

查询操作需要ResultSet对象接收查询结果

默认情况下MySQL批处理没有开启，需要在url后面添加`?rewriteBatchedStatements=true`





## 第3节 连接池

### 1 简介

连接池是创建和管理一个连接的缓冲池的技术，这些连接准备好被任何需要它们的线程使用

连接池是装有连接的容器，使用连接的话，可以从连接池中进行获取，使用完成之后将连接归还给连接池

自定义连接池的步骤：

1. 编写一个类实现DataSource接口
2. 重写getConnection方法
3. 初始化多个连接在内存中
4. 编写归还连接的方法

三种方式：

1. 继承
2. 装饰者模式：一种设计模式
3. 动态代理



### 2 装饰者模式

概念：

> 为了简化编程，提供一个模板类。模板类原封不动地将接口中的所有方法都实现，但是都没有增强。编写一个装饰类继承模板类。在装饰类中只需要增强某一个方法即可。



### 3 开源连接池

Druid的概述：可与Spring快速整合

使用方式：

1. 手动设置参数
2. 使用配置文件
   - 编写druid.properties
   - 使用Properties对象load配置文件

---

C3P0的概述：一个开源的JDBC连接池，支持Spring

使用方式：

1. 加载配置文件c3p0-config.xml
2. 加载属性文件properties





## 第4节 DbUtils

### 1 简介

DbUtils是Apache组织提供的一个对JDBC进行简单封装的开源工具类库

比如获得连接、预编译SQL、释放资源等



### 2 QueryRunner对象

在一般情况下如果执行CRUD的操作：

`QueryRunner(DataSource ds)`：构造方法

`update()`方法：实现增删改

`query()`方法：实现查询



### 3 查询

步骤：

1. 创建核心类QueryRunner
2. 执行查询



### 4 ResultSetHandler

ArrayHandler：将一条记录封装到一个Object数组中

ArrayListHandler：将多条记录封装到一个装有Object数组的List集合中

BeanHandler：将一条记录封装到一个JavaBean中

BeanListHandler：将多条记录封装到一个装有JavaBean的List集合中

MapHandler：将一条记录封装到一个Map集合中，Map的key是列名，Map的Value就是表中列的记录

MapListHandler：将多条记录封装到一个装有Map的List集合中

ColumenListHandler：将某列的值封装到List集合中

ScalarHandler：将单个值封装

KeyedHandler：将一条记录封装到一个Map集合中；将多条记录封装到一个装有Map集合的Map集合中，且外面的Map的key是可以指定的