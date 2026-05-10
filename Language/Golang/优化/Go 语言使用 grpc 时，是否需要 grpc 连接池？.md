---
title: Go 语言使用 grpc 时，是否需要 grpc 连接池？
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
---

## Go 语言使用 grpc 时，是否需要 grpc 连接池？

如果是在 grpc 上再包一层连接池肯定是不需要的，分两个层面看：

- MySQL 连接池这种是单个连接只能负载一个并发，没有可用连接时会阻塞执行，并发跟不上的时候连接池相应调大点。然而 grpc 的 HTTP2 连接有复用能力，N 个 goroutine 用一个 HTTP2 连接没有任何问题，不会单纯因为没有可用连接而阻塞执行。
- grpc 内建的 balancer 已经有很好的连接管理的支持了，每个后端实例一个 HTTP2 物理连接，而且可以用插件扩展，如果希望对单个后端实例创建多个 HTTP2 连接，小改个 balancer 插件即可。