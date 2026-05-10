---
title: JOIN算法
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: guide
status: complete
---

## JOIN算法

## 概述

在`MySQL`中，使用`Nested-Loop Join`的算法思想去优化`join`，`Nested-Loop Join`翻译成中文则是“嵌套循环连接”。

```
举个例子：

select * from t1 inner join t2 on t1.id=t2.tid

（1）t1称为外层表，也可称为驱动表。

（2）t2称为内层表，也可称为被驱动表。

在选择Join算法时，会有优先级，理论上会优先判断能否使用INLJ、BNLJ：

**Index Nested-LoopJoin > Block Nested-Loop Join > Simple Nested-Loop Join**
```

## 实现

### 简单嵌套循环连接（`Simple Nested-LoopJoin, SNLJ`）

简单嵌套循环连接实际上就是简单粗暴的嵌套循环

### 索引嵌套循环连接（`Index Nested-LoopJoin, INLJ`)

### 设计

减少内层表数据的匹配次数

索引嵌套循环连接是基于索引进行连接的算法，索引是基于内层表的，通过外层表匹配条件直接与内层表索引进行匹配，避免和内层表的每条记录进行比较， 从而利用索引的查询减少了对内层表的匹配次数，优势极大的提升了join的性能

### 使用场景

只有内层表join的列有索引时，才能用到`Index Nested-LoopJoin`进行连接。

### 缓存块嵌套循环连接（`Block Nested-Loop Join, BNLJ`）

### 设计

减少内层表数据的循环次数

缓存块嵌套循环连接通过一次性缓存多条数据，把参与查询的列缓存到Join Buffer 里，然后拿join buffer里的数据批量与内层表的数据进行匹配，从而减少了内层循环的次数（遍历一次内层表就可以批量匹配一次Join Buffer里面的外层表数据）。

### 使用场景

当不使用Index Nested-Loop Join的时候，默认使用Block Nested-Loop Join

## 附录

[学习Mysql的join算法：Index Nested-Loop Join和Block Nested-Loop Join_Java程序员的进阶之路-CSDN博客](https://blog.csdn.net/u010841296/article/details/89790399)