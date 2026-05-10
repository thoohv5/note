---
title: net/http
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
---

## net/http

`net/http` 是 Go 标准库提供的 HTTP 客户端和服务端实现，也是 Go Web 开发的基础。

### 基本用法

```go
// 简单 HTTP 服务器
http.HandleFunc("/hello", func(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("Hello, World"))
})
http.ListenAndServe(":8080", nil)
```

```go
// 自定义 Server 配置
srv := &http.Server{
    Addr:         ":8080",
    Handler:      mux,
    ReadTimeout:  5 * time.Second,
    WriteTimeout: 10 * time.Second,
    IdleTimeout:  120 * time.Second,
}
srv.ListenAndServe()
```

### 核心概念

- **Handler**: 实现 `ServeHTTP(ResponseWriter, *Request)` 接口的类型
- **ServeMux**: 默认路由器，基于路径前缀匹配
- **Middleware**: 通过包装 Handler 实现，常用于日志、认证、恢复 panic
- **Context**: 每个 `*http.Request` 自带 `context.Context`，用于超时控制和取消传播

### 注意事项

- 默认 ServeMux 不支持路径参数（如 `/user/:id`），需第三方路由
- 生产环境务必设置 `ReadTimeout`/`WriteTimeout`，否则易受慢连接攻击
- `http.Client` 连接池默认复用连接，需正确关闭 `resp.Body` 防止连接泄漏
- Go 1.22 起 ServeMux 支持 `{name}` 和 `{name...}` 模式匹配

## 相关笔记

- [[gorilla mux]] — 功能更强的第三方路由
- [[Context]] — 与 HTTP 请求深度集成

## 参考

- [Go net/http 文档](https://pkg.go.dev/net/http)