# Elasticsearch

## 1 简介

**Elasticsearch**，简称**ES**，目前性能最好的搜索引擎

- 一个**分布式**的、Restful风格的**搜索引擎**
- 支持对各种类型的数据的检索
- 搜索速度快，可以提供实时的搜索服务
- 便于水平扩展*（集群）*，每秒可以处理PB级海量数据
- 使用ES的网站有：Facebook，Github，维基百科，百度，等等



ES相关术语：

- **索引index**：对应database，6.0后对应table
- **类型type**：对应table
- **文档doc**：对应row
- **字段**：对应column
- **集群cluster**：多台服务器的分布式部署
- **节点node**：集群中的每台服务器
- **分片shard**：索引拆分成多个分片来存储，提高并发能力
- **副本replica**：对分片的备份，一个分片可以包含多个副本





## 2 安装与配置

[官方网站](https://www.elastic.co/)

[官方中文文档](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html)

使用步骤：

1. 在官方网站下载合适的版本

2. 解压缩，在config下更改elasticsearch.yml中的配置：

   - `cluster.name` 集群的名字
   - `path.data` 存储数据的路径
   - `path.logs` 存储日志的路径

3. 配置环境变量：将bin目录的路径添加到系统环境变量Path中

4. 安装对应版本的中文分词插件elasticsearch analysis ik：https://github.com/medcl/elasticsearch-analysis-ik

   下载好后解压缩到Elasticsearch目录的plugins\ik\下

5. （可选）安装postman软件：https://www.postman.com/

6. 打开Elasticsearch目录下的bin\elasticsearch.bat即启动Elasticsearch服务

*Elasticsearch默认占用9200端口用于HTTP请求，9300端口用于TCP请求*





## 3 命令

控制台输入：

- `curl -X GET "localhost:9200/_cat/health?v"` 查看ES健康状况
- `curl -X GET "localhost:9200/_cat/nodes?v"` 查看单节点的资源（CPU、内存等）占用状态
- `curl -X GET "localhost:9200/_cat/indices?v"` 查看索引
- `curl -X PUT "localhost:9200/test"` 创建名为`test`的索引
- `curl -X DELETE "localhost:9200/test"` 删除名为`test`的索引

可使用postman软件简化输入HTTP请求的过程





## 4 Spring整合Elasticsearch

Spring Boot整合Elasticsearch步骤：

1. 引入依赖：`spring-boot-starter-data-elasticsearch`
2. 在application.properties中配置Elasticsearch：
   - `cluster-name`
   - `cluster-nodes`
3. 使用API：
   - ElasticsearchTemplate
   - ElasticsearchRepository



---

注意：由于Elasticsearch和Redis底层都是Netty，启动时会报IllegalStateException异常

解决方法：在**启动类**中加上以下代码

```java
@PostConstruct
public void init() {
	// 解决netty启动冲突问题
	// 参见Netty4Utils.setAvailableProcessors()
	System.setProperty("es.set.netty.runtime.available.processors", "false");
}
```

---

