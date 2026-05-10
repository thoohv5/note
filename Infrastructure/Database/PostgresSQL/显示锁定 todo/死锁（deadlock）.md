---
title: 死锁（deadlock）
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: guide
status: complete
---

## 死锁（deadlock）

## 现象

```bash
ERROR:  deadlock detected
DETAIL:  Process 18446 waits for ShareLock on transaction 976; blocked by process 18478.
Process 18478 waits for ShareLock on transaction 975; blocked by process 18446.
HINT:  See server log for query details.
CONTEXT:  while updating tuple (0,2) in relation "t_data"
```

## 查看锁信息

```sql
SELECT virtualtransaction, relation::regclass, locktype, page, tuple, mode, granted, transactionid FROM pg_locks ORDER BY granted, virtualtransaction;
```

## 检测

死锁检测，是pg的一种自动检测机制，可以发现两个或者多个session之间对互斥资源的申请造成的死锁，并且可以随机将其中一个事务回滚掉，以解除死锁。

PG的死锁时间由deadlock_timeout参数控制，默认是1s，当发生死锁超时，会随机将一个事务回滚掉，同时记录到数据库日志中。

```bash
deadlock_timeout

## 死锁检测之前在一个锁上等待的总时间。
This is the amount of time to wait on a lock before checking to see if there is a deadlock condition.
```

## 原因

服务器会尽力确保多个事务可以并发工作而不互相干扰，但是还是会出现互相干扰的情况。为了实现事务的特性，PG为数据库中的**每个表和每一行都维护了锁**，并且每次只会将锁授予第一个事务。之后出现的所有事务都不得不等待，直到第一个事务完成，并释放它所持有的锁。未没有获取锁的事务被挂起，无法继续执行。因为锁的这种机制，所以在数据库繁忙的场景，是极易发生死锁的。

1. **因为系统资源不足**
2. **进程运行推进的顺序不合适**
3. **资源分配不当等**

## 解决办法

### 索引使用问题导致的死锁问题

索引使用存在问题的话会导致死锁问题，假设在一个数据查询的事务当中，进行数据检索的时候没办法按照SQL中的where条件进行查询，因此导致了全表扫描，那么此时数据库表的行级锁会上升为表级锁。如果此时有多个未能按照where条件进行数据查询的事务存在，那么就容易导致数据库死锁问题。也就是说在数据库表数据量比较大的时候，对应进行数据查询的表没有建立索引或者说索引创建的不合理导致无法通过索引进行数据查询，只能通过全表索引，这样的场景下就容易产生死锁。

如何避免：
在进行数据查询的时候，对应的SQL语句不宜太过复杂，也就是说尽量避免多张表的关联查询。

### 不同事务之间的访问顺序问题

当用户A 访问数据库表A时，此时对表A加了共享锁，然后又访问数据库表B。而此时另一个用户B 访问表B，对表B加了共享锁，然后试图访问表A。但是用户A由于用户B已经锁住表B，它必须等待用户B释放表B才能继续，同样用户B要等用户A释放表A才能继续，也就是说互相等待对方释放资源，从而导致了死锁的发生。

如何避免：
这种情况在实际项目中遇到的可能比较多，主要还是需要通过控制代码的执行逻辑，避免多表操作时同时锁住多个资源。

避免死锁的实战建议
（1）如果平台中存在大事务，尽量将其拆分为小事务。因为大事务一般操作的数据库表或者数据都比较多，因此造成死锁或者阻塞的概率就会相对较大。

（2）为数据库表设计合理的索引，尽量避免数据查询时索引未覆盖或者索引失效的情况，因为全表扫描会会导致给表中的数据行上锁，大大增加了数据库产生死锁的概率。

（3）如果业务允许，我们可以尝试将隔离级别调低，比如将隔离级别从RR调整为RC，可以避免掉很多因为gap锁造成的死锁。