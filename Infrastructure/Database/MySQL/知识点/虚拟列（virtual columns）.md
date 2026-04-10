# 虚拟列（virtual columns）

# 概述

**Generated Column**

在MySQL 5.7中，支持两种`Generated Column` :

- `Virtual Generated Column`只将`Generated Column`保存在数据字典中（表的元数据），并不会将这一列数据持久化到磁盘上 【默认】
- `Stored Generated Column`将Generated Column持久化到磁盘上，而不是每次读取的时候计算所得

很明显，后者存放了可以通过已有数据计算而得的数据，需要更多的磁盘空间，与Virtual Column相比并没有优势，因此，MySQL 5.7中，不指定Generated Column的类型，默认是Virtual Column。

如果需要Stored Generated Golumn的话，可能在Virtual Generated Column上建立索引更加合适

综上，一般情况下，都使用Virtual Generated Column，这也是MySQL默认的方式

**语法：**

```sql
col_name data_type [GENERATED ALWAYS] AS (expr)
  [VIRTUAL | STORED] [NOT NULL | NULL]
  [UNIQUE [KEY]] [[PRIMARY] KEY]
  [COMMENT 'string']
```

# 场景

# 附录

[MySQL :: MySQL 8.4 Reference Manual :: 15.1.20.8 CREATE TABLE and Generated Columns](https://dev.mysql.com/doc/refman/8.4/en/create-table-generated-columns.html)

[MySQL :: MySQL 8.4 Reference Manual :: 15.1.20.9 Secondary Indexes and Generated Columns](https://dev.mysql.com/doc/refman/8.4/en/create-table-secondary-indexes.html)