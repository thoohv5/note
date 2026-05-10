---
title: pg_bigm
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: reference
status: complete
source: http://pgbigm.osdn.jp/pg_bigm_en-1-2.html
---

## pg_bigm

pg_bigm 是 PostgreSQL 的**全文搜索扩展**，实现二元组（bigram）索引，与内置 `pg_trgm` 类似但支持更短的关键词（最小 2 字符，pg_trgm 为 3）。

### 安装

```sql
CREATE EXTENSION pg_bigm;
```

### 核心函数

```sql
-- 查看词的二元组分解
SELECT show_bigm('hello');  -- {" h","he","el","ll","lo","o "}

-- 相似度比较（0~1）
SELECT similarity('hello', 'hallo');  -- 0.5

-- 全文搜索
SELECT * FROM docs WHERE content LIKE '%keyword%';
-- 自动使用 bigram 索引加速
```

### 索引

```sql
-- 创建 bigram 索引加速 LIKE 和相似度搜索
CREATE INDEX idx_content_bigm ON docs USING gin (content gin_bigm_ops);
```

### 与 pg_trgm 对比

| 特性 | pg_trgm | pg_bigm |
|------|---------|---------|
| 最小关键词长度 | 3 字符 | 2 字符 |
| 非字母语言 | 支持 | 更好支持（如日文） |
| 内置 | PG 核心 | 第三方扩展 |
| 索引类型 | GIN/GiST | GIN |

### 适用场景

- 短关键词搜索（2 字符）
- 日文、中文等多字节字符搜索
- 模糊匹配和相似度计算

### 注意

- 需单独安装编译：`make && make install`
- 索引体积较大，评估存储开销

### 相关

- [[pg_trgm]] [[PostgreSQL全文搜索]] [[ltree]]