---
title: TCP 泛洪攻击（SYN）
date: 2026-04-07
tags: [理论, 网络]
type: guide
status: complete
---

# TCP 泛洪攻击（SYN）

### 问题：

SYN攻击就是Client在短时间内伪造大量不存在的IP地址，并向Server不断地发送SYN包，Server则回复确认包，并等待Cleint确认，由于源地址不存在，因此Server需要不断重发直至超时，这些伪造的SYN包将长时间占用半连接队列，导致正常的SYN请求因为队列满而被丢弃，从而引起网络拥塞甚至系统瘫痪。

### 解决方案：

1. 增加SYN连接，增加半连接队列的容量
2. 减少SYN+ACK重试次数，避免大量的超时重发
3. 利用SYN Cookie技术，在服务端接收到SYN后不立即分配连接资源，而是根据SYN计算一个Cookie流，连同第二次握手回复给客户端，在客户端回复ACK带上Cookie值，服务端验证Cookie合法之后才分配连接资源