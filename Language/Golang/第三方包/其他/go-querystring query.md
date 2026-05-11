---
title: go-querystring/query
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
---
## go-querystring/query

> Google 出品的 Go 库，将 struct 编码为 URL Query String。

### 安装

```bash
go get github.com/google/go-querystring
```

### 基本用法

```go
type Options struct {
    Query   string `url:"q"`
    ShowAll bool   `url:"all"`
    Page    int    `url:"page"`
}
opt := Options{Query: "foo", ShowAll: true, Page: 2}
v, _ := query.Values(opt)
fmt.Println(v.Encode()) // "all=true&page=2&q=foo"
```

### url tag 选项

| tag | 说明 |
|-----|------|
| `url:"q"` | 自定义字段名 |
| `url:"q,omitempty"` | 零值时省略 |
| `url:",omitempty"` | 使用默认字段名+省略 |

### 支持的字段类型

- 基本类型：string/bool/int/float
- 切片/数组
- 嵌套 struct（展开）
- time.Time（RFC3339）

### 注意

- 非标准库，需引入额外依赖
- 仅支持编码，不支持解码（需用 gorilla/schema）
- 嵌套 slice 需注意编码格式

### 参考

- [google/go-querystring](https://github.com/google/go-querystring)
- [[URL编码]]
