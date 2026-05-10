---
title: NSQ
date: 2026-04-07
tags:
  - 基础设施
  - 消息队列
type: reference
status: complete
---

## NSQ

## 概述

[https://github.com/nsqio/nsq](https://github.com/nsqio/nsq)

[NSQ Docs 1.2.1 - A realtime distributed messaging platform](https://nsq.io/)

### NSQ是一个实时的分布式的消息处理平台

### 特点

- 支持分布式的拓扑结构，没有SPOF（单点故障）
- 支持横向水平扩展（没有 brokers 等任何集中式代理）
- 性能优越，基于推送的低延迟的消息传递模式
- 结合负载均衡和多路广播类型的消息路由
- 擅长面向流(streaming)（高吞吐量）和 任务（Job）导向的工作负载
- 主要是基于内存的（超过高水位线消息将透明的存储到磁盘中）
- 运行时的服务发现
- 传输层安全性（TLS）
- 数据格式不可知
- 很少的依赖项，容易部署，具有健全的、有边界的、默认的配置项
- 简单的TCP协议，支持任何语言的客户端库
- 具有HTTP接口，用于统计、管理行为以及生产者操作（不需要客户端库额外发布）
- 集成 statsd 用于实时监测
- 强大的集群管理接口 （nsqadmin）

### 组件

- nsqd 消息队列的核心，是一个守护进程，负责接收、队列和投递信息给客户端。
- nsqlookupd 管理拓扑信息，是一个守护进程，并提供最终一致的服务发现功能。
- nsqadmin 是一个Web 管理界面，用于实时的检测集群信息，并能够执行各种管理任务

### **连接方式**

- 直连模式
- nsqlookupd连接