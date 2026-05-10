---
title: InnoDB引擎
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: guide
status: complete
---

## InnoDB引擎

### 事务

MVVC,AICD,Redo Log & Undo Log

### 锁

行级锁

### 索引

~~HASH~~，BTREE

### 存储结构：

```sql
show variables like 'innodb_file_per_table'
```

ON 独立空间：frm、tablename.ibd 

OFF 系统表空间：frm、IBDATAX

## 使用场景

1.  用来处理大量短期事务，短期事务大部分情况是正常提交，很少会被回滚
2. InnoDB适合大多数OLTP应用

### 备注

1. MySQL5.5.5版本之后的默认存储引擎

> OLTP（on-line transaction processing）翻译为联机事务处理， OLAP（On-Line Analytical Processing）翻译为联机分析处理，从字面上来看OLTP是做事务处理，OLAP是做分析处理。从对数据库操作来看，OLTP主要是对数据的增删改，OLAP是对数据的查询。
>