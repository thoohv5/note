---
title: 记录锁（Record Lock）
date: 2026-04-07
tags: [基础设施, 数据库, MySQL]
type: reference
status: incomplete
---

## 记录锁（Record Lock）

### 概述

记录锁是最常见的**行级锁**，只锁定**某一具体的记录行**。

它是临键锁的组成部分之一。

源码定义名称：LOCK_REC_NOT_GAP

对单条**索引**记录进行加锁，锁住的是索引记录而非记录本身，即使表中没有任何索引，MySQL会自动创建一个隐式的row_id作为聚集索引来进行加锁。

### 示例

```sql
-- 精确锁定主键记录
SELECT * FROM users WHERE id=5 FOR UPDATE;
```