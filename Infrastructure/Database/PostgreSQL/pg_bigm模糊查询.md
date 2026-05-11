---
title: 模糊查询（pg_bigm）
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: note
status: complete
---
## 模糊查询（pg_bigm）

> pg_bigm 是 PostgreSQL 的全文模糊搜索扩展，基于 2-gram 索引，比内置 pg_trgm 更适合非拉丁语言。

### 安装

```sql
CREATE EXTENSION pg_bigm;
```

### 创建索引

```sql
CREATE INDEX idx_name ON t USING gin (name gin_bigm_ops);
```

### pg_bigm vs pg_trgm

| 特性 | pg_bigm | pg_trgm |
|------|---------|---------|
| gram 大小 | 2-gram | 3-gram |
| 最小关键字 | 2 字符 | 3 字符 |
| 非拉丁语言 | 更适合 | 准确度较低 |
| 性能 | 索引更大但搜索更快 | 索引更小 |
| GIN 索引 | gin_bigm_ops | gin_trgm_ops |

### 常用函数

```sql
-- 相似度
SELECT show_bigm('hello world');
-- 模糊匹配
SELECT * FROM t WHERE name LIKE '%keyword%';
-- BIGM 相似度
SELECT bigm_similarity('abc', 'abcd');
-- 使用 likequery 优化多维搜索
SELECT * FROM t WHERE name = likequery('ab%c');
```

### 参考

- [pg_bigm 阿里云文档](https://help.aliyun.com/document_detail/182266.html)
- [[PostgreSQL全文搜索]]
- [[pg_trgm]]
