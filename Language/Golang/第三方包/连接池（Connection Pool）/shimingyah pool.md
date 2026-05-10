---
title: shimingyah/pool
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: reference
status: complete
source: https://github.com/shimingyah/pool
---

## shimingyah/pool

Golang 通用连接池库，支持任意实现了 `Close()` 接口的资源复用。

### 安装

```bash
go get github.com/shimingyah/pool
```

### 基本用法

```go
// 创建工厂函数
factory := func() (interface{}, error) {
    return net.Dial("tcp", "127.0.0.1:8080")
}

// 创建连接池（容量5，空闲超时60s）
p, err := pool.New(factory, 5, 60, 30*time.Second)
if err != nil { panic(err) }

// 获取连接
conn, err := p.Get()
// 归还连接
p.Put(conn)
// 关闭池
p.Close()
```

### 核心特性

- **连接复用**：空闲连接自动回收
- **超时控制**：获取连接可设等待超时
- **健康检查**：支持 Ping 函数检测连接可用性
- **并发安全**：内部使用互斥锁保护

### 适用场景

- 数据库连接复用
- gRPC 连接池
- Redis 连接管理
- 频繁短连接的 TCP 服务

### 注意

- `Put` 前需确保连接仍有效，否则应 `Close` 后重建。
- 连接池耗尽时 `Get` 会阻塞等待，注意设合理超时。

### 相关

- [[sync Pool]] [[连接池原理]]