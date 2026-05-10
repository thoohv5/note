---
title: gofiber/fiber
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
source: https://github.com/gofiber/fiber
---

## gofiber/fiber

Fiber 是一个 Go Web 框架，API 风格接近 Express。它基于 `fasthttp`，强调低分配和高性能，适合构建 HTTP API、微服务接口和轻量 Web 服务。

## 特点

- 路由、中间件、分组、静态文件等能力完整。
- API 风格简洁，迁移 Express 思维成本低。
- 底层不是标准库 `net/http`，部分标准库中间件不能直接复用。

## 基本示例

```go
package main

import "github.com/gofiber/fiber/v2"

func main() {
    app := fiber.New()

    app.Get("/ping", func(c *fiber.Ctx) error {
        return c.JSON(fiber.Map{"message": "pong"})
    })

    _ = app.Listen(":3000")
}
```

## 选型注意

- 如果项目大量依赖 `net/http` 中间件，Gin、Chi、Echo 这类标准库兼容框架迁移成本更低。
- 如果追求极致吞吐并能接受 `fasthttp` 生态差异，Fiber 是可选方案。
- 生产环境需要统一处理超时、日志、Recover、限流和请求体大小限制。

## 参考

- [gofiber/fiber](https://github.com/gofiber/fiber)
