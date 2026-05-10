---
title: Kong
date: 2026-04-07
tags: [微服务, 分布式, API网关]
type: reference
status: complete
source: https://www.jianshu.com/p/a68e45bcadb6
---

### Kong

### 概述

Kong 是基于 OpenResty/Nginx 的 API 网关，主要负责统一入口、路由转发、认证鉴权、限流、熔断、日志和可观测性插件扩展。它通过插件机制把通用网关能力从业务服务中抽离出来。

### 架构要点

- 数据面基于 Nginx/OpenResty 处理高并发请求。
- 控制面负责配置管理，包括服务、路由、上游和插件。
- 插件可运行在请求生命周期中，例如认证、限流、日志、Header 改写。
- 可以使用数据库模式，也可以使用 DB-less 模式管理配置。

### 核心概念

- Service：后端服务抽象，描述真实上游地址。
- Route：匹配入口请求，例如 Host、Path、Method。
- Upstream：上游负载均衡对象，可关联多个 Target。
- Plugin：附加能力，例如 JWT、Key Auth、Rate Limiting、Prometheus。

### 适用场景

- 微服务统一 API 入口。
- 多语言服务统一认证、限流和日志。
- 对外开放 API 时统一管理访问策略。

### 注意事项

- 插件过多会增加请求链路开销，需要压测确认容量。
- 网关是核心入口，配置变更必须有灰度和回滚策略。
- 需要和服务发现、监控告警、日志系统一起设计。

### 附录

- [Kong 介绍](https://www.jianshu.com/p/a68e45bcadb6)
