---
title: alibabacloud-go/rocketmq-20220801
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
source: https://github.com/alibabacloud-go/rocketmq-20220801
---

## alibabacloud-go/rocketmq-20220801

> 阿里云 RocketMQ 5.x 官方 Go SDK，用于操作商业版 RocketMQ 实例。

### 安装

```bash
go get github.com/alibabacloud-go/rocketmq-20220801
```

### 核心概念

| 概念 | 说明 |
|------|------|
| Instance | RocketMQ 实例（商业版 5.x） |
| Topic | 消息主题 |
| ConsumerGroup | 消费者组 |
| Producer | 消息生产者 |
| Tag | 消息标签（二级分类） |

### 使用场景

- 阿里云 RocketMQ 商业版 5.x 实例
- 需要原生集成阿里云 RAM 鉴权
- 使用 OpenAPI 管理 Topic/Group 等资源

### 注意

- 该 SDK 是商业版 SDK，非开源 RocketMQ 客户端
- 开源 RocketMQ Go 客户端可使用 `github.com/apache/rocketmq-client-go/v2`
- 需要配置阿里云 AccessKey 进行鉴权

### 参考

- [GitHub - alibabacloud-go/rocketmq-20220801](https://github.com/alibabacloud-go/rocketmq-20220801)
- [[RocketMQ]]