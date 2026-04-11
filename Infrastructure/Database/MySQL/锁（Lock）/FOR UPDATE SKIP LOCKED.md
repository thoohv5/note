---
title: FOR UPDATE SKIP LOCKED
date: 2026-04-11
tags: [基础设施, 数据库, MySQL]
type: note
status: incomplete
---

# 概述

语法：select语句后跟 for update skip locked
作用：目标对象没有被其它会话加锁则可加锁，被其它会话加了锁就跳过。
解决问题：用来避免锁资源竞争引起的阻塞。
适用场景：
    - 多用于MySQL作为消息队列的存储组件，特别是消费者数量>1的场景，多个worker进程同时检测任务队列中的任务，确保每个任务只被一个worker处理，同时其它worker不会被阻塞（这里的worker进程，可以是不断循环查询队列任务的过程，用毫秒级到分钟级的死循环检测表里是否有要处理的队列任务，加锁是为了避免多个消费者同时处理同一个队列任务引发的并发问题）。
    - 换句话说：加锁的目的是为了避免多个消费者同时消费相同的数据造成重复消费，加skip locked的目的是为了保证**其它进程**每次扫描到加锁的数据时跳开，避免因排它锁阻塞，保证探测任务快速执行。
注意：
    - 不适用与并发条件下加锁保证数据不出错的场景，因为它遇见目标数据加锁就跳过。（MySQL官方文档原文：Queries that skip locked rows return an inconsistent view of the data. SKIP LOCKED is therefore not suitable for general transactional work. However, it may be used to avoid lock contention when multiple sessions access the same queue-like table.）。
    - 它能跳过仅仅是它能跳过，不代表没加skip locked的X或S锁遇见这个锁不会发生阻塞。
    - **MySQL8才有的特性，5.7会报错**：You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'skip locked' at line 1。

# 实操
### 创建表结构
```sql
CREATE TABLE `jobs` ( 
	`id` bigint unsigned NOT NULL AUTO_INCREMENT, 
	`queue` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '不同渠道队列执行的优先级，可以是high，default，low等。', 
	`payload` longtext CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '队列进程所需要的数据，序列化后的json字符串，包含了框架所需要的序列化数据和业务数据。',
	`attempts` tinyint unsigned NOT NULL COMMENT '记录任务已经被尝试执行的次数。每次任务失败后，attempts的值会递增。当attempts达到设定的最大重试次数时，任务将被标记为失败。', 
	`reserved_at` int unsigned DEFAULT NULL COMMENT '表示任务被锁定（reserved）的时间戳。当一个worker开始处理任务时，任务会被锁定，以防止其它 worker同时处理相同的任务。reserved_at存储的是记录被锁定时的时间戳。', 
	`available_at` int unsigned NOT NULL COMMENT '记录任务应该变为可执行状态的时间戳。将任务推送到队列时，可以选择延迟任务的执行时间。', 
	`created_at` int unsigned NOT NULL COMMENT '队列被创建的时间戳。', 
	PRIMARY KEY (`id`) USING BTREE, 
	KEY `jobs_queue_index` (`queue`) USING BTREE 
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci ROW_FORMAT=DYNAMIC;
```


### 添加数据
```sql
INSERT INTO `jobs` (`id`, `queue`, `payload`, `attempts`, `reserved_at`, `available_at`, `created_at`) VALUES (1, 'default', '这里是要被处理的任务：xxxxx', 1, NULL, 1735097169, 1735097169);
INSERT INTO `jobs` (`id`, `queue`, `payload`, `attempts`, `reserved_at`, `available_at`, `created_at`) VALUES (2, 'default', '这里是要被处理的任务：xxxxx', 1, NULL, 1735097179, 1735097179);
INSERT INTO `jobs` (`id`, `queue`, `payload`, `attempts`, `reserved_at`, `available_at`, `created_at`) VALUES (3, 'default', '这里是要被处理的任务：xxxxx', 1, NULL, 1735097189, 1735097189);
```

### 执行SQL
```sql
select * from `jobs` where `queue` = 'high' and ((`reserved_at` is null and `available_at` <= 1735097879) or (`reserved_at` <= 1735094259)) order by `id` asc limit 1 FOR UPDATE SKIP LOCKED  
select * from `jobs` where `queue` = 'default' and ((`reserved_at` is null and `available_at` <= 1735097879) or (`reserved_at` <= 1735094259)) order by `id` asc limit 1 FOR UPDATE SKIP LOCKED  
select * from `jobs` where `queue` = 'low' and ((`reserved_at` is null and `available_at` <= 1735097879) or (`reserved_at` <= 1735094259)) order by `id` asc limit 1 FOR UPDATE SKIP LOCKED

```

### 验证
|步骤|会话1|会话2|说明|
|---|---|---|---|
|1|start transaction;|start transaction;|双方开启事务|
|2|select * from `jobs` where `queue` = 'default' and ((`reserved_at` is null and `available_at` <= 1735097879) or (`reserved_at` <= 1735094259)) order by `id` asc limit 1 FOR UPDATE SKIP LOCKED|/|会话1返回id为1的数据|
|3|/|select * from `jobs` where `queue` = 'default' and ((`reserved_at` is null and `available_at` <= 1735097879) or (`reserved_at` <= 1735094259)) order by `id` asc limit 1 FOR UPDATE SKIP LOCKED|会话2返回id为2的数据，直接跳过加锁的1|
|4|update jobs set reserved_at = UNIX_TIMESTAMP() where id = 1;|update jobs set reserved_at = UNIX_TIMESTAMP() where id = 2;|将找到的任务标记为队列正在处理|
|5|commit;|commit;|提交事务，结束流程|
# 附录

[MySQL for update skip locked 与 for update nowait - 小松聊PHP进阶 - 博客园](https://www.cnblogs.com/phpphp/p/18630869)
[https://dev.mysql.com/doc/refman/8.0/en/innodb-locking-reads.html#innodb-locking-reads-for-update](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking-reads.html#innodb-locking-reads-for-update)