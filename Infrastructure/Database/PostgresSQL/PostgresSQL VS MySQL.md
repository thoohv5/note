---
title: PostgresSQL VS MySQL
date: 2026-04-07
tags: [基础设施, 数据库]
type: guide
status: complete
---

# PostgresSQL VS MySQL

[PostgreSQL 与 MySQL 相比，优势何在？](https://www.zhihu.com/question/20010554)

1. PG text类型不区分大小,MySQL 需要手动区分 small text, middle text 
2. PG 可以设置 transform_null_equals配置，转换 = null to 为 is null
3. MySQL 需要utf8mb4才能显示emoji
4. MySQL 事务隔离级别Repeatable read 需要加锁才能解决幻读，PG 隐藏乐观锁不需要加锁解决幻读
5. MySQL 不支持多个表从同一个序列中取ID
6. MySQL 不支持OVER子句