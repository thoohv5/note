---
title: 二进制日志（binlog）
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: guide
status: complete
---

## 二进制日志（binlog）

## 定义

用于记录数据库执行的写入性操作（不包括查询）信息，以二进制的形式保存在磁盘中。逻辑日志，并且由服务层进行记录，使用任何存储引擎的MySQL数据库都会记录binlog日志。

<aside>
💡 逻辑日志：以SQL语句的方式记录的数据
物理日志：数据页的变更

</aside>

binlog 是可以追加写入的。==“追加写”==是指 binlog 文件写到一定大小后会切换到下一个，并不会覆盖以前的日志。

## 作用

1. 主从复制
2. 数据恢复

## 过程

对于InnoDB存储引擎而言，只有在事务提交时才会记录binlog，此时记录还在内存中，MySQL通过sync_binlog参数控制binlog的刷盘时机

```go
sync_binlog
```

- 0：不去强制要求，由系统自行判断何时写入磁盘；
- 1：每次 `commit` 的时候都要将 `binlog` 写入磁盘； (5.7.7)
- N：每N个事务，才会将 `binlog` 写入磁盘。

## 日志格式

[[Infrastructure/Database/MySQL/高性能MySQL/复制/复制（Replication）|复制（Replication）]]
