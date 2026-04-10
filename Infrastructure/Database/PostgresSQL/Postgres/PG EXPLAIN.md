---
title: PG EXPLAIN
date: 2026-04-07
tags: [基础设施, 数据库]
type: reference
status: complete
---

# PG EXPLAIN

# 命令

```
EXPLAIN [ ( option [, ...] ) ] statement
EXPLAIN [ ANALYZE ] [ VERBOSE ] statement

where option can be one of:

    ANALYZE [ boolean ]
    VERBOSE [ boolean ]
    COSTS [ boolean ]
    BUFFERS [ boolean ]
    TIMING [ boolean ]
    SUMMARY [ boolean ]
    FORMAT { TEXT | XML | JSON | YAML }

```

## 参数说明

- **ANALYZE 选项为TRUE 会实际执行SQL，并获得相应的查询计划，默认为FALSE。如果优化一些修改数据的SQL 需要真实的执行但是不能影响现有的数据，可以放在一个事务中，分析完成后可以直接回滚。**
- VERBOSE 选项为TRUE 会显示查询计划的附加信息，默认为FALSE。附加信息包括查询计划中每个节点（后面具体解释节点的含义）输出的列（Output），表的SCHEMA 信息，函数的SCHEMA 信息，表达式中列所属表的别名，被触发的触发器名称等。
- COSTS 选项为TRUE 会显示每个计划节点的预估启动代价（找到第一个符合条件的结果的代价）和总代价，以及预估行数和每行宽度，默认为TRUE。
- BUFFERS 选项为TRUE 会显示关于缓存的使用信息，默认为FALSE。该参数只能与ANALYZE 参数一起使用。缓冲区信息包括共享块（常规表或者索引块）、本地块（临时表或者索引块）和临时块（排序或者哈希等涉及到的短期存在的数据块）的命中块数，更新块数，挤出块数。
- TIMING 选项为TRUE 会显示每个计划节点的实际启动时间和总的执行时间，默认为TRUE。该参数只能与ANALYZE 参数一起使用。因为对于一些系统来说，获取系统时间需要比较大的代价，如果只需要准确的返回行数，而不需要准确的时间，可以把该参数关闭。
- SUMMARY 选项为TRUE 会在查询计划后面输出总结信息，例如查询计划生成的时间和查询计划执行的时间。当ANALYZE 选项打开时，它默认为TRUE。
- FORMAT 指定输出格式，默认为TEXT。各个格式输出的内容都是相同的，其中XML | JSON | YAML 更有利于我们通过程序解析SQL 语句的查询计划，为了更有利于阅读，我们下文的例子都是使用TEXT 格式的输出结果。

# 节点类型

## 控制节点（Control Node)

## 扫描节点（ScanNode)

扫描节点，简单来说就是为了扫描表的元组，每次获取一条元组（Bitmap Index Scan除外）作为上层节点的输入。当然严格的说，不光可以扫描表，还可以扫描函数的结果集、链表结构、子查询结果集等。

目前在PostgreSQL 中支持：

- Seq Scan，顺序扫描
- Index Scan，基于索引扫描，但不只是返回索引列的值
- IndexOnly Scan，基于索引扫描，并且只返回索引列的值，简称为覆盖索引
- BitmapIndex Scan，利用Bitmap 结构扫描
- BitmapHeap Scan，把BitmapIndex Scan 返回的Bitmap 结构转换为元组结构
- Tid Scan，用于扫描一个元组TID 数组
- Subquery Scan，扫描一个子查询
- Function Scan，处理含有函数的扫描
- TableFunc Scan，处理tablefunc 相关的扫描
- Values Scan，用于扫描Values 链表的扫描
- Cte Scan，用于扫描WITH 字句的结果集
- NamedTuplestore Scan，用于某些命名的结果集的扫描
- WorkTable Scan，用于扫描Recursive Union 的中间数据
- Foreign Scan，用于外键扫描
- Custom Scan，用于用户自定义的扫描

## 物化节点（Materialization Node)

## 连接节点（Join Node)

# 附录

[https://blog.csdn.net/weixin_41287260/article/details/124394206](https://blog.csdn.net/weixin_41287260/article/details/124394206)