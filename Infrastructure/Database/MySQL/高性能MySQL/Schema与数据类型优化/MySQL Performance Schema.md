---
title: MySQL Performance Schema
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: note
status: incomplete
---

## MySQL Performance Schema

> 目的
> 

关注性能数据

> 配置
> 

默认情况下，性能模式处于启用状态。

```bash
[mysqld]
performance_schema=ON
```

```bash
**SHOW VARIABLES LIKE 'performance_schema';**
```