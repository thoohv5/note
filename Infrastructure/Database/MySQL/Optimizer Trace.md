---
title: Optimizer Trace
date: 2026-04-07
tags: [基础设施, 数据库, MySQL]
type: reference
status: complete
---

## Optimizer Trace

## 概述

OPTIMIZER_TRACE是MySQL 5.6引入的一项跟踪功能，它可以跟踪优化器做出的各种决策（比如访问表的方法、各种开销计算、各种转换等），并将跟踪结果记录到 `INFORMATION_SCHEMA.OPTIMIZER_TRACE`表中。

EXPLAIN可以帮助您查看查询是否进行全表扫描，还是进行稍微优化的索引扫描。但EXPLAIN并没有真正告诉你MySQL为什么这样做。而Optimizer Trace旨在生成人类和程序可读的输出，以帮助理解[MySQL优化](https://so.csdn.net/so/search?q=MySQL优)96&spm=1001.2101.3001.7020器所做的决策和操作。

## 使用

```sql
## 默认是关闭的
SELECT @@optimizer_trace;

SET SESSION OPTIMIZER_TRACE="enabled=on";

SELECT * FROM information_schema.OPTIMIZER_TRACE
```

### 系统变量

### `optimizer_trace`

- enabled：是否开启optimizer_trace；on表示开启，off表示关闭。
- one_line：是否开启单行存储。on表示开启；off表示关闭，将会用标准的JSON格式化存储。设置成on将会有良好的格式，设置成off可节省一些空间。

## 优化过程

- prepare 阶段
- optimize 阶段
- execute 阶段

## 附录

[MySQl查询分析工具 Optimizer Trace-CSDN博客](https://blog.csdn.net/qq_40926260/article/details/142596943)

[](https://blog.51cto.com/u_15077536/10052884)