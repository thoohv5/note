---
title: RDB视图
date: 2026-04-07
tags: [基础设施, 数据库]
type: note
status: incomplete
---

# RDB视图

### RDB 视图？

**RDB 视图（Relational Database View）**，是关系型数据库中一种虚拟表（virtual table）。

它本质上是一个 **SQL 查询的封装**，你把它当成一张“表”来用，但它并不真正存储数据，而是**在你查询的时候临时计算**出来的。