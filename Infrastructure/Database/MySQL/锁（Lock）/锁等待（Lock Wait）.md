---
title: 锁等待（Lock Wait）
date: 2026-04-07
tags: [基础设施, 数据库, MySQL]
type: note
status: complete
---

## 流程

### 1. 排队

加锁（表锁或行锁）不能立即获得时，事务进入锁等待状态。

- InnoDB 每个事务预创建 8 个表锁结构 + 8 个行锁结构
- 不够时动态创建新的锁结构
- 锁结构 `type_mode` 第 9 位置 1 表示等待状态
- 表锁加入表对象的 locks 链表末尾
- 行锁加入 rec_hash 同一哈希槽链表末尾

### 2. 登记

InnoDB 从 `waiting_threads` 内存区域的 `srv_slot_t` 槽位中找空闲 slot：

- `in_use` 设为 true
- 记录超时时间 `wait_timeout`
- 通知后台线程发生锁等待

### 3. 等待与通知

- 行锁：记录开始时间（用于超时计算）
- 表锁：由 server 层记录开始时间

被通知的条件：
- 锁等待超时（`innodb_lock_wait_timeout`，默认 50s）
- 其他事务释放锁后获得锁
- 死锁检测中被选为受害者

## 排查

```sql
-- 查看当前锁等待
SELECT * FROM performance_schema.data_lock_waits;

-- 查看 InnoDB 事务状态
SHOW ENGINE INNODB STATUS\G

-- 查看锁等待超时设置
SHOW VARIABLES LIKE 'innodb_lock_wait_timeout';
```

## 参考

- [MySQL 核心模块揭秘 | 锁等待](https://cloud.tencent.com/developer/article/2451339)
- [[Infrastructure/Database/MySQL/锁（Lock）/死锁（Dead Lock）|死锁]]
- [[Infrastructure/Database/MySQL/锁（Lock）/锁类型（Lock Type）|锁类型]]