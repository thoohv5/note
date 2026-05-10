---
title: gRPC
date: 2026-04-07
tags:
  - 微服务
  - DevOps
type: reference
status: complete
---

## gRPC

进行API开发选gRPC还是HTTP APIs?

gRPC 基于 HTTP/2 标准设计，带来诸如双向流、流控、头部压缩、单 TCP 连接上的多复用请求等特

降低（de）序列化成本，自动类型检查，形式化API以及更少的TCP管理开销

### gRPC 允许你定义四类服务方法

- 单项 RPC，即客户端发送一个请求给服务端，从服务端获取一个应答，就像一次普通的函数调用。

```
rpc SayHello(HelloRequest) returns (HelloResponse){
}
```

- 服务端流式 RPC，即客户端发送一个请求给服务端，可获取一个数据流用来读取一系列消息。客户端从返回的数据流里一直读取直到没有更多消息为止。

```
rpc LotsOfReplies(HelloRequest) returns (stream HelloResponse){
}
```

- 客户端流式 RPC，即客户端用提供的一个数据流写入并发送一系列消息给服务端。一旦客户端完成消息写入，就等待服务端读取这些消息并返回应答。

```
rpc LotsOfGreetings(stream HelloRequest) returns (HelloResponse) {
}
```

- 双向流式 RPC，即两边都可以分别通过一个读写数据流来发送一系列消息。这两个数据流操作是相互独立的，所以客户端和服务端能按其希望的任意顺序读写，例如：服务端可以在写应答前等待所有的客户端消息，或者它可以先读一个消息再写一个消息，或者是读写相结合的其他方式。每个数据流里消息的顺序会被保持。

```
rpc BidiHello(stream HelloRequest) returns (stream HelloResponse){
}
```

## 附录

[如何在Kubernetes实现gRPC的负载平衡？](https://www.jdon.com/50653)

[gRPC 官方文档中文版_V1.0](http://doc.oschina.net/grpc?t=58010)

## The C based gRPC (C++, Python, Ruby, Objective-C, PHP, C#)

[grpc/grpc](https://github.com/grpc/grpc)

## PHP Protobuf - Google's Protocol Buffers for PHP

[allegro/php-protobuf](https://github.com/allegro/php-protobuf)