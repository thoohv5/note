---
title: ACID测试
date: 2026-04-07
  - 基础设施
  - 数据库
type: note
status: complete
---

## ACID测试

> 事物隔离级别 测试
> 

```bash
## read-uncommitted 未提交读
## read-committed 已提交读
## repeatable-read 可重复读
## serializable 可串行化

## 自动事物
autocommit=1

show global variables like '%autocommit%';
set global autocommit = 0;
show global variables like '%autocommit%';

show global variables like '%isolation%';
set global transaction_isolation ='read-uncommitted';
show global variables like '%isolation%';

show global variables like '%isolation%';
set global transaction_isolation ='read-committed';
show global variables like '%isolation%';

show global variables like '%isolation%';
set global transaction_isolation ='repeatable-read';
show global variables like '%isolation%';
```