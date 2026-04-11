---
title: GRPC内置方案
date: 2026-04-07
tags: [微服务, 分布式中间件]
type: guide
status: complete
---

# GRPC内置方案

gRPC 的内置方案如下图所示：

![](https://i-blog.csdnimg.cn/blog_migrate/3562c0bf6103a34dd4f63f642e076a28.png)

gRPC 在官网文档中提供了实现 LB 的思路，并在不同语言的 gRPC 代码 API 中已提供了命名解析和负载均衡接口供扩展。默认提供了 DNS-resolver 的实现，接口相当规范，实现起来也不复杂，只需要实现服务注册（Registry）和服务监听 + 解析（Watcher+Resolver）的逻辑就行了，这里简单介绍其基本实现过程：

1. 构建注册中心，这里注册中心一般要求具备分布式一致性（满足 CAP 定理的 AP 或 CP）的高可用的组件集群，如 Zookeeper、Consul、Etcd 等
2. 构建 gRPC 服务端的注册逻辑，服务启动后定时向注册中心注册自身的关键信息（一般开启新的 groutine 来完成），至少包含 IP 和端口，其他可选信息，如自身的负载信息（CPU 和 Memory）、当前实时连接数等，这些辅助信息有助于帮助系统更好的执行 LB 算法
3. gRPC 客户端向注册中心发出服务解析请求，注册中心将请求中关联的所有服务的信息返回给 gRPC 客户端，客户端与所有在线的服务建立起 HTTP2 长连接
4. gRPC 客户端发起 RPC 调用，根据 LB 均衡器中实现的负载均衡策略（gRPC 中默认提供的算法是 RoundRobin），选择其中一 HTTP2 长连接进行通信，即 LB 策略决定哪个子通道 - 即哪个 gRPC 服务器将接收请求

### **gRPC 负载均衡的运行机制**

gRPC 提供了负载均衡实现的用户侧接口，我们可以非常方便的定制化业务的负载均衡策略，为了理解 gRPC 的负载均衡的实现机制，后续博客中我会分析下 `gRPC` 实现负载均衡的代码。

![](https://i-blog.csdnimg.cn/blog_migrate/852c3bdd8de20849709c106d30dda048.png)

1. Resolver
    - 解析器，用于从注册中心实时获取当前服务端的列表，同步发送给 Balancer
2. Balancer
    - 平衡器，一是接收从 Resolver 发送的服务端列表，建立并维护（长）连接状态；二是每次当 Client 发起 Rpc 调用时，按照一定算法从连接池中选择一个连接进行 Rpc 调用
3. Register
    - 注册，用于服务端初始化和在线时，将自己信息上报到注册中心，主要信息有 Ip，端口等