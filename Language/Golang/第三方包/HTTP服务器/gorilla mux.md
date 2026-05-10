---
title: gorilla/mux
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
source: "https://github.com/gorilla/mux"
---

## gorilla/mux

`gorilla/mux` 是 Go 社区最流行的 HTTP 路由器，提供了比标准 `net/http.ServeMux` 更强大的路由匹配能力。

### 核心功能

- **路径变量**: `{name}` 和 `{name:regex}` 模式
- **方法限制**: `.Methods("GET", "POST")` 限定 HTTP 方法
- **子路由**: `.Subrouter()` 创建路由组
- **中间件**: `.Use()` 注册中间件

```go
r := mux.NewRouter()
r.HandleFunc("/users/{id:[0-9]+}", getUser).Methods("GET")
api := r.PathPrefix("/api").Subrouter()
api.Use(loggingMiddleware)
```

### 与 net/http 对比

| 特性 | net/http | gorilla/mux |
|------|----------|-------------|
| 路径参数 | ❌ (1.22+ 支持) | ✓ |
| 正则匹配 | ❌ | ✓ |
| 方法过滤 | 需手动 `if` | `.Methods()` |
| 子路由 | ❌ | `.Subrouter()` |
| 依赖 | 标准库 | 第三方 |

### 注意事项

- gorilla/mux 已于 2022 年底归档，推荐评估替代方案如 `go-chi/chi`、`gin`、`echo`
- Go 1.22+ 标准库路由功能已大幅增强，小型项目可不再依赖第三方路由

## 相关笔记

- [[net http]] — Go 标准库 HTTP 基础
- [[gin]] — 全功能 Web 框架

## 参考

- [gorilla/mux GitHub](https://github.com/gorilla/mux)