---
title: Apollo
date: 2026-04-07
tags:
  - 微服务
  - 分布式
  - 配置中心
type: reference
status: complete
source: https://github.com/apolloconfig/apollo
---

## Apollo

### 概述

Apollo 是携程开源的分布式配置中心，用于集中管理不同应用、不同环境、不同集群的配置。配置发布后，客户端 SDK 可以实时获取最新配置，适用于微服务、多环境、多集群的统一配置管理。

### 核心能力

- 统一管理多环境、多集群、多命名空间配置。
- 配置发布后实时推送到客户端，支持热更新。
- 配置发布版本化，支持回滚。
- 支持灰度发布，可先对部分实例生效再全量发布。
- 支持配置项全局搜索，便于排查配置使用位置。
- 支持权限管理、发布审批和操作审计。
- 支持 Java、.NET 原生 SDK，并提供 HTTP API 及多语言第三方 SDK。

### 核心概念

- App：接入 Apollo 的应用。
- Environment：环境，例如 DEV、FAT、UAT、PRO。
- Cluster：同一环境下的集群，用于区分机房、地域或部署单元。
- Namespace：配置命名空间，用于隔离不同类型或不同模块的配置。
- Release：一次配置发布，对应可审计、可回滚的版本。

### 架构要点

- 服务端基于 Spring Boot 和 Spring Cloud 开发，不依赖外部应用容器。
- 主要外部依赖是 MySQL，用于保存配置、发布记录、审计等数据。
- 客户端 SDK 不强依赖具体业务框架，Java SDK 可独立运行，也支持 Spring 和 Spring Boot。
- 非 Java/.NET 应用可以通过 HTTP API 或第三方 SDK 接入。

### 适用场景

- 微服务配置集中管理。
- 多环境配置差异管理。
- 需要配置热更新、灰度发布、回滚和审计的系统。
- 需要统一治理配置权限和发布流程的团队。

### 使用注意

- 配置中心属于基础设施，需要优先保障高可用和备份恢复能力。
- 生产环境配置发布应配合审批、灰度和回滚流程。
- 敏感配置应结合权限控制和加密策略，避免把密钥当普通配置裸存。
- 客户端应设计合理的本地缓存和默认值，避免配置中心异常导致业务不可用。

### 附录

- [Apollo GitHub](https://github.com/apolloconfig/apollo)
- [Apollo 官方文档](https://www.apolloconfig.com/#/zh/README)
