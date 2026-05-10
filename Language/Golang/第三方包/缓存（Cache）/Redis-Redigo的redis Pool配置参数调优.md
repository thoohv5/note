---
title: Redigo Redis Pool 配置参数调优
date: 2026-04-07
tags: [编程语言, Golang, 第三方包]
type: reference
status: complete
source: http://www.mamicode.com/info-detail-2056965.html
---

# Redigo Redis Pool 配置参数调优

## 概念
Redigo 是 Go 语言 Redis 客户端，Pool 是连接池，合理配置可平衡性能与资源消耗。

## 核心参数
| 参数 | 说明 | 建议值 |
|------|------|--------|
| MaxIdle | 最大空闲连接数 | = MaxActive |
| MaxActive | 最大活跃连接数 | 按并发量设置 |
| IdleTimeout | 空闲连接超时 | 240s |
| Wait | 连接池耗尽时是否等待 | true（推荐） |
| MaxConnLifetime | 连接最大存活时间 | 0(不限) |
| TestOnBorrow | 取出时检测可用性 | 开发true，线上false |

## 调优建议
- MaxIdle 不应太少，避免频繁建立连接
- Wait=true 可避免连接耗尽错误
- IdleTimeout 设置适中，避免空闲连接被 Redis 服务端断开
- TestOnBorrow 开启会带来额外 PING 开销
- 使用 Dial 函数配置连接超时和密码

## 场景
高性能 Go 应用中管理 Redis 连接复用。

## 参考
[[go-redis redis]], [[Redis Pool]]
