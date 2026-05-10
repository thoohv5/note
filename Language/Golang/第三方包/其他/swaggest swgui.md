---
title: swaggest/swgui
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: reference
status: complete
source: https://github.com/swaggest/swgui
---

## swaggest/swgui

swgui 是一个 Go 语言 Swagger UI 嵌入式中间件，可将 Swagger/OpenAPI 文档直接内嵌在二进制中，无需额外部署静态文件。

### 安装

```bash
go get github.com/swaggest/swgui/v4
```

### 基本用法

```go
import "github.com/swaggest/swgui/v4emb"

// v4emb 将 Swagger UI v4 嵌入二进制
handler := v4emb.New("My API", "/swagger.json", "/docs")
http.ListenAndServe(":8080", handler)
```

### 特性

- **支持多版本**：Swagger UI v3、v4、v5
- **零外部依赖**：UI 资源编译进二进制
- **自定义标题**：可设置页面标题
- **支持 OpenAPI 3.0**：兼容 swagger.json / openapi.json
- **`embed` 原生**：利用 Go 1.16+ embed 特性

### 适用场景

- 微服务 API 文档内嵌
- 减少生产环境外部静态资源依赖
- 与 go-swagger、swag 等代码生成工具配合

### 注意

- 嵌入 UI 会增加约 1-2MB 二进制体积。
- JSON spec 文件需单独提供路径。

### 相关

- [[Swaggo生成API文档]] [[OpenAPI]]