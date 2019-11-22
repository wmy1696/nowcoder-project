# Kafka，构建TB级异步消息系统

## 1. 阻塞队列

* BlockingQueue
  * 解决线程通信的问题。
  * 阻塞方法：put、take。

![avatar](img\20191114195406.png)

* 生产者消费者模式
  * 生产者：产生数据的线程。
  * 消费者：使用数据的线程。

* 实现类
  * ArrayBlockingQueue
  * LinkedBlockingQueue
  * PriorityBlockingQueue、SynchronousQueue、DelayQueue等。

## 2. Kafka入门

* Kafka简介
  * Kafka是一个分布式的流媒体平台。
  * 应用：消息系统、日志收集、用户行为追踪、流式处理。
* Kafka特点
  * 高吞吐量、消息持久化、高可靠性、高扩展性。
* Kafka术语
  * Broker、Zookeeper
  * Topic、Partition、Offset
  * Leader Replica 、Follower Replica

## 3. Spring整合Kafka

* 引入依赖
  * spring-kafka
* 配置Kafka
  * 配置server、consumer
* 访问Kafka
  * 生产者
    kafkaTemplate.send(topic, data);
  * 消费者
    @KafkaListener(topics = {"test"})
    public void handleMessage(ConsumerRecord record) {}

## 4. 发送系统通知

* 触发事件
  * 评论后，发布通知
  * 点赞后，发布通知
  * 关注后，发布通知
* 处理事件
  * 封装事件对象
  * 开发事件的生产者
  * 开发事件的消费者

## 5. 显示系统通知

* 通知列表
  * 显示评论、点赞、关注三种类型的通知
* 通知详情
  * 分页显示某一类主题所包含的通知
* 未读消息
  * 在页面头部显示所有的未读消息数量