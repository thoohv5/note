---
title: 间隙锁（Gap Lock）
date: 2026-04-07
tags: [基础设施, 数据库]
type: reference
status: complete
---

# 间隙锁（Gap Lock）

## 概述

源码定义名称：LOCK_GAP

封锁记录中的间隔，防止间隔中被其他事务插入。

间隙锁主要出现在RR隔离级别，避免出现幻读。

虽然在RR隔离级别默认使用`Gap Lock`，但用户可以通过以下两种方式来显式地关闭Gap Lock：

1）将事务的隔离级别设置为READ COMMITTED；

2）将参数innodb_locks_unsafe_for_binlog设置为1；

当设置了上述参数或隔离级别调整到**READ COMMITTED**时，除了外键约束和唯一性检查（duplicate key）依然需要Gap Lock，其余情况仅使用Record Lock进行锁定。但需要知道的是，上述设置破坏了事务的隔离性，并且对于MySQL复制来说，可能会导致主从数据的不一致。虽然MySQL目前默认隔离级别是RR，但是基本生产环境标配基本都是RC隔离级别+ROW格式。

## 示例

```sql
SELECT * FROM users WHERE salary BETWEEN 5000 AND 8000 FOR UPDATE;
```