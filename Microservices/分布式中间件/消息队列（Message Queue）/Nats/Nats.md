---
title: Nats
date: 2026-04-07
tags: [微服务, 分布式中间件]
type: reference
status: complete
---

# Nats

# **NATS JetStream的设计目标**

- The system must be easy to configure and operate and be observable.
    
    系统必须容易配置，易于操作，易于被发现或者被观察。
    
- The system must be secure and operate well with NATS 2.0 security models.
    
    系统必须是安全的，并且基于NATS2.0的安全模型能够良好的运行。
    
- The system must be scale horizontally and be applicable to a high ingestion rate.
    
    系统必须支持水平扩展，支持高吞吐。
    
- The system must support multiple use cases.
    
    系统必须支持多种用户场景。
    
- The system must self-heal and always be available.
    
    系统必须能自我修复，并且保持高可用
    
- The system must have an API that is closer to core NATS.
    
    系统必须和我们前面介绍的这个NATS部分的API保持一致。
    
- The system must allow NATS messages to be part of a stream as desired. The system must display payload agnostic behavior.
    
    系统必须能够复用Core NATS，之前的一些这个，消息，并且具备系统这种负载无关的特性。
    
- The system must not have third party dependencies.
    
    系统不能依赖第三方软件
    

# 附录

[https://docs.nats.io/](https://docs.nats.io/)[https://github.com/nats-io/go-nats-examples](https://github.com/nats-io/go-nats-examples)