---
title: PostgreSQL C语言扩展开发
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: reference
status: complete
source: https://zhuanlan.zhihu.com/p/548554847
---

## PostgreSQL C 扩展开发

PostgreSQL 支持用 **C 语言** 编写自定义函数和扩展，可以直接操作内部数据结构，获得最高性能。

### 基本结构

```c
#include "postgres.h"
#include "fmgr.h"

PG_MODULE_MAGIC;

PG_FUNCTION_INFO_V1(add_ab);

Datum add_ab(PG_FUNCTION_ARGS) {
    int32 a = PG_GETARG_INT32(0);
    int32 b = PG_GETARG_INT32(1);
    PG_RETURN_INT32(a + b);
}
```

### 编译与安装

```sql
-- 编译为共享库
-- gcc -shared -o add.so add.c -I/path/to/pg/include

-- 在数据库中注册
CREATE FUNCTION add_ab(integer, integer) RETURNS integer
    AS 'add.so', 'add_ab'
    LANGUAGE C STRICT;
```

### 扩展（Extension）结构

1. **Makefile** — 用 PGXS 构建系统
2. **控制文件** (`.control`) — 扩展元信息
3. **SQL 文件** — 定义函数、类型、操作符

### 优势

- 性能最高，直接访问内部数据结构
- 可调用 PostgreSQL 内部 SPI 执行 SQL
- 适用于计算密集型、内存敏感操作

### 注意

- C 函数中的错误（如空指针）可能导致进程崩溃。
- 升级 PostgreSQL 大版本需重新编译。
- 优先使用 PL/pgSQL、PL/Python 等高级语言，仅在性能关键路径使用 C。

### 相关

- [[PLpgSQL PERFORM忽略返回值]] [[pgtt]] [[ltree]]