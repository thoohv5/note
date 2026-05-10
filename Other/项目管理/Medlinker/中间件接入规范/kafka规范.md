---
title: kafka规范
date: 2026-04-07
tags:
  - 其他
  - 项目管理
type: guide
status: complete
---

## kafka规范

## 1.名词解释

Broker：Kafka集群包含一个或多个服务器，这种服务器被称为broker

Topic：Kafka消息的类别

Partition：是Kafka中横向扩展和一切并行化的基础，每个Topic都至少被切分为1个Partition

Offset：消息在Partition中的编号，编号顺序不跨Partition

Consumer：用于从Broker中取出/消费Message。

Producer：用于往Broker中发送/生产Message。

Replication：Kafka支持以Partition为单位对Message进行冗余备份，每个Partition都可以配置至少1个Replication(当仅1个Replication时即仅该Partition本身)。

Leader：每个Replication集合中的Partition都会选出一个唯一的Leader，所有的读写请求都由Leader处理。其他Replicas从Leader处把数据更新同步到本地，过程类似大家熟悉的MySQL中的Binlog同步。

ISR(In-Sync Replica)：是Replicas的一个子集，表示目前Alive且与Leader能够“Catch-up”的Replicas集合。由于读写都是首先落到Leader上，所以一般来说通过同步机制从Leader上拉取数据的Replica都会和Leader有一些延迟(包括了延迟时间和延迟条数两个维度)，任意一个超过阈值都会把该Replica踢出ISR。每个Partition都有它自己独立的ISR。

## 2.consumer使用规范

1. consumer的owner线程需确保不会异常退出，避免客户端无法发起消费请求，阻塞消费。
2. 确保处理完消息后再做消息commit，避免业务消息处理失败，无法重新拉取处理失败的消息。
3. consumer不能频繁加入和退出group，频繁加入和退出，会导致consumer频繁做rebalance，阻塞消费。
4. consumer数量不能超过topic分区数，否则会有consumer拉取不到消息。
5. consumer需周期poll，维持和server的心跳，避免心跳超时，导致consumer频繁加入和退出，阻塞消费。
6. consumer拉取的消息本地缓存应有大小限制，避免OOM（Out of Memory）。
7. consumer session设置为30秒，[session.timeout.ms](http://session.timeout.ms/)=30000。
8. Kafka不能保证消费重复的消息，业务侧需保证消息处理的幂等性。
9. 消费线程退出要调用consumer的close方法，避免同一个组的其他消费者阻塞[sesstion.timeout.ms](http://sesstion.timeout.ms/)的时间。

## 3.producer使用规范

1. 同步复制客户端需要配合使用：ack=all
2. 配置发送失败重试：retries=3
3. 发送优化：[linger.ms](http://linger.ms/)=0
4. 生产端的JVM内存要足够，避免内存不足导致发送阻塞

## 4.topic使用规范

配置要求：推荐3副本，同步复制，最小同步副本数为2，且同步副本数不能等于topic副本数，否则宕机1个副本会导致无法生产消息。

创建方式：支持选择是否开启kafka自动创建Topic的开关。选择开启后，表示生产或消费一个未创建的Topic时，会自动创建一个包含3个分区和3个副本的Topic。

单topic最大分区数建议为20。

topic副本数为3（当前版本限制，不可调整）。

## 5.其他建议

连接数限制：3000

消息大小：不能超过10MB

使用sasl_ssl协议访问Kafka：确保DNS具有反向解析能力，或者在hosts文件配置kafka所有节点ip和主机名映射，避免Kafka client做反向解析，阻塞连接建立。

磁盘容量申请超过业务量 * 副本数的2倍，即保留磁盘空闲50%左右。

业务进程JVM内存使用确保无频繁FGC，否则会阻塞消息的生产和消费。

如果同一业务，生产者比较小，则可以合并生产者。通过‘atype’的方式，即在消费体指定类型，消费端去处理类型进行消费。

## 6.命名规范【建议】

分隔符：只能用'_'，以英文字符开头，不允许出现其他特殊符号

业务名称之前:'_b' ,标识业务,business

如果指定了生产的分区则是：_p10,标识第10个partition

consumer_group,允许一个consumer_group通过业务订阅多个topic，减少consumer_group在业务层的维护成本

topic: 服务名称_b_业务名称，例如：med-im需要产生一个群组退出的topic,指定在第2个分区→ med_im_b_quit_group_p2

consumer_group：服务名称_b_业务名称，例如med-im需要订阅用户相关app的更新消息，但是这部分包含多个topic,但是我只订阅第20个partion。就是→med_im_b_user_update_p20

如果consumer_group,自己的一个服务只想维护一个，则是服务名称为_b_all，例如:med_im_b_all

## 7.消费规范

1. 不允许跨app(仓库)消费，用其他仓库的consumer_group消费topic给自己用
2. 消费者原则上，自己app(仓库)的消费者，只能写在自己维护的项目中
3. 如果需要消费爬坡，则需要自己处理好消息幂等这种关系
4. producer可能会出现丢失，请重要数据一定要先落库，再生产
5. 消费者如果出现了互相影响的问题，则考虑删除消费者/合并topic的方式进行处理