I---
layout: post
title: kafka consumer
category: kafka
tags: kafka
description: kafka consumer
---

# kafka consumer
```
 brew install kafka
```
安装后提示

> zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties; kafka-server-start /usr/local/etc/kafka/server.properties


# 运行
```
sh kafka-server-start /usr/local/etc/kafka/server.properties
```

# 创建topic
```
sh kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```

# 显示topic列表
```
sh kafka-topics --list --zookeeper localhost:2181
```

# 使用kafka发送消息
Kafka带有一个命令行客户端，它接受文件输入或者标准输入，然后将输入信息作为消息发送到Kafka集群。默认情况下，每行输入都被作为一条单独的消息。
请运行该producer，然后输入一些消息到控制台，该producer会将这些消息发送到kafka服务器。

```
./kafka-console-producer --broker-list localhost:9092 --topic test 
this is a message
xiongfeng's message
```

# 使用Consumer将消息输出
Kafka还有一个consumer命令行客户端，它会将消息搬运到标准输出。

```
./kafka-console-consumer --zookeeper localhost:2181 --topic test --from-beginning
this is a message
xiongfeng's message
```

# 多个broker的配置
复制`/usr/local/etc/kafka/server.properties`文件

```
cp /usr/local/etc/kafka/server.properties server-1.properties
cp /usr/local/etc/kafka/server.properties server-2.properties
```

编辑这些属性

```
server-1.properties:
    broker.id=1
    port=9093
    log.dir=/tmp/kafka-logs-1
```
启动

```
sh kafka-server-start /usr/local/etc/kafka/server-1.properties
```

查看

```
kafka-topics --describe --zookeeper localhost:2181 --topic topic
```

结果

```
Topic:topic	PartitionCount:1	ReplicationFactor:3	Configs:
	Topic: topic	Partition: 0	Leader: 0	Replicas: 2,0,1	Isr: 0,2,1

```
下面是上述输出的解释。第一行是所有分区的汇总信息;其它行,每行显示一个分区的信息。由于这个topci只有一个分区，因此，只有一行。
"leader" 是负责某个特定分区的所有读写操作的那个节点。每个节点都是"被随机选择的一部分分区"的leader。
"replicas" 是由"复制了该分区日志的那些节点"组成的列表，而不论它们是否是leader，甚至也不论它们当前是否活着。
"isr" 是那些"同步中(in-sync)"的副本(replicas)。它是replicas列表的子集，包含的是当前活着的、而且跟上leader节奏的那些replicas。


kafka 存储原理

打开server.properties 中kafka 配置的日志文件地址

```
/usr/local/var/lib/kafka-logs/
```

## kafka 删除topic

```
sh kafka-topics --delete --zookeeper localhost:2181 --topic topic1

```

```
Topic topic1 is marked for deletion.
Note: This will have no impact if delete.topic.enable is not set to true.
```

需要将配置文件 `/usr/local/etc/kafka/server.properties` 中`delete.topic.enable` 设置为true

## kafka 修改topic
```
bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic my-topic
    --config max.message.bytes=128000
```
## kafka 分区
Kafka分配Replica的算法如下：

将所有Broker（假设共n个Broker）和待分配的Partition排序
将第i个Partition分配到第（i mod n）个Broker上
将第i个Partition的第j个Replica分配到第（(i + j) mode n）个Broker上


## kafka group


## kafka monitor
```
java -cp KafkaOffsetMonitor-assembly-0.2.1.jar com.quantifind.kafka.offsetapp.OffsetGetterWeb --zk localhost:2181 --port 8080 --refresh 10.seconds --retain 2.days
```
地址[kafka-offset-monitor-graphite](http://https://github.com/allegro/kafka-offset-monitor-graphite)



