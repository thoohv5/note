---
title: 死锁（Dead Lock）
date: 2026-04-07
  - 基础设施
  - 数据库
type: reference
status: complete
---

## 死锁（Dead Lock）

## 概述

死锁是指两个或者多个事务在执行过程中，因互相等待对方持有的资源而无法继续执行的情况。或者举例说明：事务A锁定资源X，然后尝试锁定资源Y，同时事务B锁定资源Y，然后尝试锁定资源X，这样就产生了死锁

## 实验

表结构

```sql
CREATE TABLE `t_order` (
`order_id` int(11) NOT NULL,
`order_addr` varchar(255) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

客户端1

```sql
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;
insert into t_order(order_id,order_addr)
select '12345',sleep(10) from dual where not exists
(select order_id from t_order where order_id='12345');
```

客户端2

```sql
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;
insert into t_order(order_id,order_addr)
select '12345',234 from dual where not exists
(select order_id from t_order where order_id='12345');
```

现象

```sql
[Err] 1213 - Deadlock found when trying to get lock; try restarting transaction
```