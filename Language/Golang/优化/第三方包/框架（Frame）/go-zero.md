---
title: go-zero
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: reference
status: complete
---

## go-zero

## 概述

go-zero 是一个集成了各种工程实践的 web 和 rpc 框架。通过弹性设计保障了大并发服务端的稳定性，经受了充分的实战检验。

go-zero 包含极简的 API 定义和生成工具 goctl，可以根据定义的 api 文件一键生成 Go, iOS, Android, Kotlin, Dart, TypeScript, JavaScript 代码，并可直接运行。

## 安装

```
go get -u github.com/zeromicro/go-zero

```

### goctl

```
go install github.com/zeromicro/go-zero/tools/goctl@latest

goctl -v

```

```
goctl api -o shorturl.api

goctl rpc -o transform.proto

```

## 工程维度

```
.
├── consumer			队列消费服务
├── go.mod
├── internal			工程内部可访问的公共模块
│   └── model
├── job						cron job 服务
├── pkg						工程外部可访问的公共模块
├── restful				HTTP 服务目录，下存放以服务为维度的微服务
├── script				脚本服务目录，下存放以脚本为维度的服务
└── service				gRPC 服务目录，下存放以服务为维度的微服务

```

## 服务纬度

```
example														单个服务目录，一般是某微服务名称
├── etc														静态配置文件目录
│   └── example.yaml
├── main.go												程序启动入口文件
└── internal											单个服务内部文件，其可见范围仅限当前服务
    ├── config										静态配置文件对应的结构体声明目录
    │   └── config.go
    ├── handler										目录，可选，一般 http 服务会有这一层做路由管理，handler 为固定后缀
    │   ├── xxxhandler.go
    │   └── xxxhandler.go
    ├── logic											业务目录，所有业务编码文件都存放在这个目录下面，logic 为固定后缀
    │   └── xxxlogic.go
    ├── svc												依赖注入目录，所有 logic 层需要用到的依赖都要在这里进行显式注入
    │   └── servicecontext.go
    └── types											结构体存放目录
        └── types.go

```

## 附录

[https://go-zero.dev/docs/tasks](https://go-zero.dev/docs/tasks)