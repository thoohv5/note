---
title: ALTER TABLE Statement
date: 2026-04-07
tags: [基础设施, 数据库, MySQL]
type: note
status: complete
---

## ALTER TABLE Statement

```bash
ALTER TABLE table_name
  [alter_action options], ...
```

其中 `alter_action` 是一个修改动作，包括

`ADD` [关键字](https://so.csdn.net/so/search?q=关键)97&spm=1001.2101.3001.7020可用来添加列、索引、约束等，包括：

- `ADD [COLUMN]`: 添加列
- `ADD INDEX`: 添加索引
- `ADD PRIMARY KEY`: 添加主键
- `ADD FOREIGN KEY`: 添加外键
- `ADD UNIQUE INDEX`: 添加唯一索引
- `ADD CHECK`: 添加检查约束

`DROP` 关键字可用来删除列、索引、约束等，包括：

- `DROP [COLUMN] col_name`: 删除列
- `ADD INDEX index_name`: 删除索引
- `DROP PRIMARY KEY`: 删除主键
- `DROP FOREIGN KEY fk_symbol`: 删除外键
- `DROP CHECK symbol`: 删除检查约束

> `MODIFY` 关键字用来修改列的定义。与 `CHANGE` 关键字不同，它不能重命名列。例如: `MODIFY [COLUMN] col_name column_definition`。
> 
> 
> `CHANGE` 关键字用来修改列的定义。与 `MODIFY` 关键字不同，它可以重命名列。例如: `CHANGE [COLUMN] old_col_name new_col_name column_definition`。
> 

`RENAME` 关键字可以重命名列、索引和表。包括：

- `RENAME COLUMN old_col_name TO new_col_name`: 重命名列。
- `RENAME INDEX old_index_name TO new_index_name`: 重命名索引。
- `RENAME new_tbl_name`: 重命名表。

### for explain

```sql
## 添加一行
ALTER TABLE user ADD name VARCHAR(20) DEFAULT 'xx' COMMENT 'yy' AFTER 字段;
ALTER TABLE supplier ADD is_third_party tinyint(2) NOT NULL DEFAULT '0' COMMENT '是否第三方（0: 本地，1：第三方）' AFTER 'delivery_cycle_desc';

## 添加多行
ALTER TABLE user ADD age INT, ADD email VARCHAR(50);

## 重命名列
ALTER TABLE user RENAME COLUMN name TO username;

## 修改列
ALTER TABLE user MODIFY username VARCHAR(45);

ALTER TABLE user CHANGE username name VARCHAR(30);
```