---
title: offset性能问题
date: 2026-04-07
  - 基础设施
  - 数据库
type: reference
status: complete
---

## offset性能问题

type: Post
status: Published
date: 2025/02/26
summary: 使用LIMIT和OFFSET在处理大量数据时可能导致查询性能下降，建议采用延迟关联的方案以提高效率。实验展示了不同SQL查询方案的性能对比。
tags: MySQL, 理论
category: Assembly

## 概述

### 语法

```sql
SELECT * FROM table_references [LIMIT {[offset,] row_count | row_count OFFSET offset}]
```

### 等价SQL

```bash
SELECT * FROM table_name LIMIT row_count;
SELECT * FROM table_name LIMIT offset, row_count;
SELECT * FROM table_name LIMIT row_count OFFSET offset;
```

<aside>
💡 offset，row_count 非负整数常量

</aside>

## 实验

### 数据结构

```sql
CREATE TABLE `user` (
  `id` bigint unsigned NOT NULL AUTO_INCREMENT COMMENT '主键',
  `identity` char(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL COMMENT '标识',
  `name` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL COMMENT '名称',
  `head_portrait` varchar(1024) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL COMMENT '头像',
  `type` tinyint unsigned NOT NULL DEFAULT '0' COMMENT '类型：XXX',
  `is_disable` tinyint unsigned NOT NULL DEFAULT '0' COMMENT '是否禁用：1-禁用，0-正常，默认：0',
  `created_at` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `updated_at` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  `deleted_at` datetime DEFAULT NULL COMMENT '删除时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uidx_identity` (`identity`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci COMMENT='用户表';
```

### 实验结果

**方案一**

```sql
SELECT * FROM `user` LIMIT 80000, 10;
+----+-------------+-------+------------+------+---------------+------+---------+------+--------+----------+-------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows   | filtered | Extra |
+----+-------------+-------+------------+------+---------------+------+---------+------+--------+----------+-------+
|  1 | SIMPLE      | user  | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 127686 |   100.00 | NULL  |
+----+-------------+-------+------------+------+---------------+------+---------+------+--------+----------+-------+
```

**方案二**

```sql
SELECT * FROM `user` LIMIT 10 OFFSET 80000;
+----+-------------+-------+------------+------+---------------+------+---------+------+--------+----------+-------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows   | filtered | Extra |
+----+-------------+-------+------------+------+---------------+------+---------+------+--------+----------+-------+
|  1 | SIMPLE      | user  | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 127686 |   100.00 | NULL  |
+----+-------------+-------+------------+------+---------------+------+---------+------+--------+----------+-------+
```

**方案三**

```sql
SELECT a.* FROM `user` AS a JOIN `user` AS b ON b.id = a.id LIMIT 10 OFFSET 80000;
+----+-------------+-------+------------+--------+---------------+---------+---------+-----------+--------+----------+-------------+
| id | select_type | table | partitions | type   | possible_keys | key     | key_len | ref       | rows   | filtered | Extra       |
+----+-------------+-------+------------+--------+---------------+---------+---------+-----------+--------+----------+-------------+
|  1 | SIMPLE      | a     | NULL       | ALL    | PRIMARY       | NULL    | NULL    | NULL      | 127686 |   100.00 | NULL        |
|  1 | SIMPLE      | b     | NULL       | eq_ref | PRIMARY       | PRIMARY | 8       | test.a.id |      1 |   100.00 | Using index |
+----+-------------+-------+------------+--------+---------------+---------+---------+-----------+--------+----------+-------------+
```

**方案四**

```sql
SELECT a.* FROM `user` AS a JOIN (SELECT id FROM `user` LIMIT 10 OFFSET 80000) AS b ON b.id = a.id;
+----+-------------+------------+------------+--------+---------------+---------+---------+------+--------+----------+-------------+
| id | select_type | table      | partitions | type   | possible_keys | key     | key_len | ref  | rows   | filtered | Extra       |
+----+-------------+------------+------------+--------+---------------+---------+---------+------+--------+----------+-------------+
|  1 | PRIMARY     | <derived2> | NULL       | ALL    | NULL          | NULL    | NULL    | NULL |  80010 |   100.00 | NULL        |
|  1 | PRIMARY     | a          | NULL       | eq_ref | PRIMARY       | PRIMARY | 8       | b.id |      1 |   100.00 | NULL        |
|  2 | DERIVED     | user       | NULL       | index  | NULL          | PRIMARY | 8       | NULL | 127686 |   100.00 | Using index |
+----+-------------+------------+------------+--------+---------------+---------+---------+------+--------+----------+-------------+
```

使用`LIMIT offset, row_count` 或 `LIMIT row_count OFFSET offset`, 会导致查询查找`offset + row_count`行，其中0到`offset`行被丢弃，`offset`到`offset+row_count`被返回。按照SQL的执行顺序，

```sql
(9) SELECT 
(10) DISTICNCT column,
(6) AGG_FUNC(column or expiression), ...
(1) FROM left_table
    (3) JOIN right_table
    (2) ON tablename.column = 
        other_tablename.column
(4) WHERE constraint_expiression
(5) GROUP BY column
(7) WITH CUBE|ROLLUP
(8) HAVING constraint_expiression
(11)ORDER BY column ASC|DESC
(12)LIMIT count OFFSET count;
```

`LIMIT` 和 `OFFSET` 在最后（12）执行，在回表之前（10），所以含有`LIMIT` 或 `OFFSET`的操作，在**跨度很大且查询数据很多**的情况，会很慢，基于以上的分析，我们应该采用**延迟关联**，**方案四**。

## 附录

[MySQL :: MySQL 8.0 Reference Manual :: 13.2.10 SELECT Statement](https://dev.mysql.com/doc/refman/8.0/en/select.html)