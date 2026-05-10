---
title: nacos-sdk-go
date: 2026-04-07
tags: [编程语言, Golang, 第三方包]
type: reference
status: complete
source: https://github.com/nacos-group/nacos-sdk-go
---

# nacos-sdk-go

## 概念
Nacos Go SDK，用于从 Go 应用连接 Nacos 服务，支持服务发现和动态配置管理。

1.3k+ stars。

## 安装
go get -u github.com/nacos-group/nacos-sdk-go/v2
要求 Go >= 1.15，Nacos >= 2.x

## 核心配置
- ClientConfig：命名空间、超时、日志、缓存目录
- ServerConfig：Nacos 服务地址、端口、GRPC 端口（默认 server+1000）

## 服务发现
namingClient.RegisterInstance(...)     // 注册实例
namingClient.DeregisterInstance(...)   // 注销
namingClient.SelectInstances(...)      // 获取健康实例
namingClient.SelectOneHealthyInstance(...) // WRR 负载均衡
namingClient.Subscribe(...)            // 监听服务变化

## 动态配置
configClient.PublishConfig(...)  // 发布配置
configClient.GetConfig(...)      // 获取配置
configClient.DeleteConfig(...)   // 删除配置
configClient.ListenConfig(...)   // 监听配置变更

## 场景
Go 微服务注册到 Nacos、动态配置热更新、服务发现与负载均衡

## 注意事项
- 支持阿里云 ACM（需 AccessKey/SecretKey）
- 多 ServerConfig 可实现故障转移
- 一个 ClientConfig 对应一个 namespace
