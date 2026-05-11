---
title: sqlc
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
source: "https://docs.sqlc.dev"
---

## sqlc

sqlc 从 SQL 语句生成**类型安全的 Go 代码**，无需手写 ORM 样板代码。

### 安装

```bash
go install github.com/sqlc-dev/sqlc/cmd/sqlc@latest
```

### 工作流程

1. 在 `schema.sql` 中定义表结构
2. 在 `query.sql` 中编写 SQL 查询，通过 `-- name: <FuncName> :<result>` 注释标记
3. 配置 `sqlc.yaml` 指定生成目录和引擎
4. 运行 `sqlc generate`，生成参数化、类型安全的 Go 函数

### 配置示例

```yaml
version: "2"
sql:
  - engine: "postgresql"
    queries: "query.sql"
    schema: "schema.sql"
    gen:
      go:
        package: "db"
        out: "db"
        emit_json_tags: true
        emit_interface: true
```

### 查询注释格式

```sql
-- name: GetUser :one
SELECT * FROM users WHERE id = $1;

-- name: ListUsers :many
SELECT * FROM users ORDER BY name;

-- name: CreateUser :exec
INSERT INTO users (name, email) VALUES ($1, $2);
```

`:one` 返回单行，`:many` 返回多行，`:exec` 无返回值，`:execrows` 返回影响行数。

### 优势

- 编译时 SQL 校验，无运行时 ORM 魔法
- 零反射，性能和手写代码一致
- 支持 PostgreSQL / MySQL / SQLite
- 生成 `*Queries` 和 `*sql.Tx` 兼容接口

### 注意事项

- 动态 WHERE 条件需通过 CASE/COALESCE 或构建多个查询处理
- 复杂 JOIN 场景建议配合 `sqlx` 使用

### 相关笔记

- [[go-sql-driver mysql]]
- [[gorm]]