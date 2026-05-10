---
title: go-sql-driver/mysql
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
source: https://github.com/go-sql-driver/mysql
---

## go-sql-driver/mysql

`go-sql-driver/mysql` 是 Go `database/sql` 的 MySQL 驱动，纯 Go 实现，支持 MySQL、MariaDB 以及部分兼容数据库，是 Go 项目连接 MySQL 的常用底层驱动。

## 安装

```bash
go get -u github.com/go-sql-driver/mysql
```

## 基本用法

```go
import (
    "database/sql"
    "time"

    _ "github.com/go-sql-driver/mysql"
)

func OpenDB() (*sql.DB, error) {
    db, err := sql.Open("mysql", "user:password@tcp(127.0.0.1:3306)/app?parseTime=true&loc=Local")
    if err != nil {
        return nil, err
    }

    db.SetConnMaxLifetime(3 * time.Minute)
    db.SetMaxOpenConns(10)
    db.SetMaxIdleConns(10)

    return db, db.Ping()
}
```

## DSN

DSN 基本格式：

```text
[username[:password]@][protocol[(address)]]/dbname[?param1=value1&param2=value2]
```

常用参数：

- `parseTime=true`：把 `DATE`、`DATETIME` 扫描为 `time.Time`。
- `loc=Local`：设置 `time.Time` 使用的时区。
- `timeout=5s`：连接超时。
- `readTimeout=5s`：读超时。
- `writeTimeout=5s`：写超时。
- `charset=utf8mb4`：字符集设置。
- `tls=true`：启用 TLS。

## 连接池建议

`database/sql` 自身负责连接池，驱动文档建议显式配置：

- `SetConnMaxLifetime`：应短于 MySQL、代理或负载均衡的连接回收时间。
- `SetMaxOpenConns`：限制最大连接数，避免压垮数据库。
- `SetMaxIdleConns`：通常设置为接近 `SetMaxOpenConns`，减少频繁建连。

## 注意事项

- `sql.Open` 不会立即建立连接，需要 `Ping` 或实际查询验证连接可用。
- 使用 `QueryContext`、`ExecContext` 支持超时和取消。
- `interpolateParams=true` 可减少部分预处理往返，但要注意字符集限制和注入风险。
- `multiStatements=true` 默认关闭，需要明确评估安全性。
- `LOAD DATA LOCAL INFILE` 需要文件 allowlist，不建议开启 `allowAllFiles=true`。

## 关联

- [[database sql]]
- [[MySQL]]
- [[连接池]]
