# Kafka

## 1 Kafka入门

### Kafka介绍

Kafka是一个开源的分布式流式处理平台，基于ZooKeeper进行管理，主要的功能包括消息中间件、日志收集、用户行为追踪、流式处理等

特点：高吞吐量、消息持久化、高可靠性、高扩展性

[Apache Kafka官方网站](https://kafka.apache.org/)



### 体系结构

- **Producer**：生产者
  - 发送主题
- **Consumer**：消费者
  - 订阅主题并消费
  - 读取消息时按偏移量（Offset）读取
- **Broker**：服务代理节点
- **Topic**：主题
  - 消息以主题为单位进行归类
- **Partition**：分区
  - 一个主题可以分为多个分区



### 环境配置

以windows系统为例，开启Kafka步骤：

1. 官网下载tgz包并解压

2. 按需求配置config\下zookeeper.properties和server.properties，如 `dataDir` 和 `log.dirs`

3. 开启命令行1，在kafka目录下输入：

   `bin\windows\zookeeper-server-start.bat config\zookeeper.properties`

4. 开启命令行2，在kafka目录下输入：

   `bin\windows\kafka-server-start.bat config\server.properties`

5. 开启命令行3，创建topic

   `bin\windows\kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1  --topic test`



> 注意：在windows的命令行里启动kafka之后，当关闭命令行窗口时，就会强制关闭kafka。这种关闭方式为暴力关闭，很可能会导致kafka无法完成对日志文件的解锁。届时，再次启动kafka的时候，就会提示日志文件被锁，无法成功启动。
>
> 解决方案：
>
> - 出现这种情况时将kafka的日志文件全部删除，再次启动即可。
>
> - 不要暴力关闭kafka，通过在命令行执行kafka-server-stop命令来关闭它
> 
> 在Linux上部署kafka之后，采用后台运行的方式，就会避免这样的问题



kafka默认端口：9092

查看当前服务器的主题：

`kafka-topics.bat --list --bootstrap-server localhost:9092`

创建生产者，往指定主题上发消息：

`kafka-console-producer.bat --broker-list localhost:9092 --topic test`

创建消费者：

`kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic test --from-beginning`

关闭zookeeper服务器：

`zookeeper-server-stop.bat`

关闭kafka服务器：

`kafka-server-stop.bat`



### Spring整合Kafka

Spring整合Kafka步骤：

1. pom.xml引入依赖

   ```xml
   <!-- 对于SpringBoot而言version标签可以略去，交给SpringBoot底层自行决定版本 -->
   		<dependency>
   			<groupId>org.springframework.kafka</groupId>
   			<artifactId>spring-kafka</artifactId>
   		</dependency>
   ```

2. 配置application.properties

   ```properties
   # KafkaProperties
   # 默认端口
   spring.kafka.bootstrap-servers-localhost:9092
   # 跟config/consumer.properties一致
   spring.kafka.consumer.group-id=test-consumer-group
   # 自动提交
   spring.kafka.consumer.enable-auto-commit=true
   # 自动提交频率为3s
   spring.kafka.consumer.auto-commit-interval=3000
   ```

3. 在启动zookeeper和kafka并创建主题后，编写代码以访问Kafka



发送异步消息测试代码：

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@ContextConfiguration(classes = CommunityApplication.class)
public class KafkaTests {
    @Autowired
    private KafkaProducer kafkaProducer;

    @Test
    public void testKafka() {
        kafkaProducer.sendMessage("test", "你好");
        kafkaProducer.sendMessage("test", "在吗");
        // 阻塞10s以等待消息打印完毕
        try {
            Thread.sleep(1000 * 10);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

}

@Component
class KafkaProducer {
    // 生产者需要的工具KafkaTemplate已被SpringBoot的容器整合
    @Autowired
    private KafkaTemplate kafkaTemplate;

    public void sendMessage(String topic, String content) {
        kafkaTemplate.send(topic, content);
    }
}

@Component
class KafkaConsumer {
    // 监听线程
    @KafkaListener(topics = {"test"})
    public void handleMessage(ConsumerRecord record) {
        System.out.println(record.value());
    }
}
```

