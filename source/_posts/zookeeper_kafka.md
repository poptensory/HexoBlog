---
title: Zookeeper和Kafka命令
categories:
- 大数据
tags:
- Zookeeper
- Kafka
toc: true
---
<Excerpt in index | > 
记录命令<!-- more -->
<The rest of contents | 余下全文>
# Zookeeper的启动与关闭
```
# 在zookeeper/bin目录下运行
./zkServer.sh start
# jps可以查看到zookeeper对应的QuorumPeerMain进程

# 查看该节点是leader/follower
./zkServer.sh status

# 关闭zookeeper
./zkServer.sh stop
```

# Kafka的启动与关闭
```
# 在kafka/bin目录下，启动Kafka
./kafka-server-start.sh config/server.properties &
# jps可以查看kafka对应的Kafka进程

# 关闭Kafka进程
./kafka-server-stop.sh
```

# 一个简单的启动消费者和生产者实例
```
# 在kafka/bin目录下
# 创建一个topic
./kafka-topics.sh --zookeeper master:2181,slave:2181 --topic test1 --replication-factor 2 --partitions 1 --create
# 查看topic状态
./kafka-topics.sh --zookeeper master:2181,slave:2181 --topic test1 --describe

# 启动一个producer
./kafka-console-producer.sh --broker-list master:9092,slave:9092 --topic test1

# 启动一个consumer
./kafka-console-consumer.sh --bootstrap-server master:9092,slave:9092 --from-beginning --topic test1
# 注意：低版本Kafka启动consumer的命令为：kafka-console-consumer.sh --zookeeper master:2181,slave:2181 --from-beginning --topic test1

# 都启动之后，在producer终端发布消息，在consumer终端可以消费消息
```