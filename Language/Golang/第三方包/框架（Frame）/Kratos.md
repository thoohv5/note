---
title: Kratos
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
---
## Kratos

> B 站开源 Go 微服务框架，基于 Protobuf 定义服务，提供 HTTP/gRPC 双协议支持。

### 核心理念

- **Wire** 依赖注入自动生成
- **Protobuf** 服务定义 + 代码生成
- **Middleware** 插件化中间件链
- **Registry** 服务注册与发现

### 快速开始

```bash
kratos new my-service
cd my-service && kratos run
```

### 分层架构

| 层 | 说明 |
|----|------|
| API | Protobuf 服务定义 |
| Service | 业务逻辑编排 |
| Biz | 业务领域逻辑 |
| Data | 数据访问层 |
| Server | HTTP+gRPC 入口 |

### 与旧版差异

- blademaster → gin
- warden → gRPC
- paladin → 配置中心抽象
- wire 依赖注入替代 dig

### 参考

- [go-kratos/kratos](https://github.com/go-kratos/kratos)
- [[微服务框架]]
- [[gRPC]]
