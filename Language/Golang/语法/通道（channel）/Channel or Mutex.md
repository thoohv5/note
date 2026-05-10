---
title: Channel or Mutex
date: 2026-04-07
tags: [编程语言, Golang, 语法]
type: note
status: complete
---

## Channel or Mutex

```go
// 不要通过共享内存来通信，而是通过通信来实现内存共享
Do not communicate by sharing memory; instead, share memory by communicating

```

## Channel

场景：数据流动

```go
// 传递数据的所有权
passing ownership of data 

// 分发任务单元
distributing units of work

// 交流异步结果
communicating async results
```

<aside>
💡 结合 golang 和 Wait Group

</aside>

## Mutex

场景：数据固定

```go
// 缓存
caches

// 状态
state
```

## 附录

[MutexOrChannel · golang/go Wiki](https://github.com/golang/go/wiki/MutexOrChannel)

[Golang并发：再也不愁选channel还是选锁_lessisbetter的博客-CSDN博客](https://blog.csdn.net/m0_43499523/article/details/86483484)