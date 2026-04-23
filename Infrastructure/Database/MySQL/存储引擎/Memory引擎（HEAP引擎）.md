---
title: Memory引擎（HEAP引擎）
date: 2026-04-07
  - 基础设施
  - 数据库
type: guide
status: incomplete
---

## Memory引擎（HEAP引擎）

### 事务

### 锁

表级锁

### 索引

HASH，BTREE

### 储存结构

frm

### 使用场景

1. 用于查找或者是映射表，例如邮编和地区对
2. 用于保存数据分析中间产生的中间表
3. 用于缓存周期性聚合数据的结果表

### 备注

1. 所有字段都为固定长度
2. 不支持BLOG和TEXT等大字段
3. 重启结构保存，数据丢失