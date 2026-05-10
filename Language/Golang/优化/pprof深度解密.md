---
title: 深度解密 Go 语言之 pprof - qcrao 的博客
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
source: https://qcrao.com/post/dive-into-go-pprof/
---

## 深度解密 Go 语言之 pprof - qcrao 的博客

`pprof` 是 Go 常用的性能剖析工具，可以采集和分析 CPU、内存、goroutine、block、mutex 等 profile，用于定位性能瓶颈、内存泄漏和并发阻塞问题。

## 常见 Profile

- `cpu`：采样 CPU 执行热点，适合定位计算密集型瓶颈。
- `heap`：查看堆内存分配和存活对象，适合定位内存增长。
- `goroutine`：查看 goroutine 堆栈，适合排查泄漏和阻塞。
- `block`：查看 goroutine 阻塞等待情况。
- `mutex`：查看锁竞争情况。
- `threadcreate`：查看系统线程创建情况。

## HTTP 暴露方式

```go
import _ "net/http/pprof"

func main() {
    go func() {
        _ = http.ListenAndServe("127.0.0.1:6060", nil)
    }()

    // app logic
}
```

常用访问路径：

```text
/debug/pprof/
/debug/pprof/profile?seconds=30
/debug/pprof/heap
/debug/pprof/goroutine
/debug/pprof/block
/debug/pprof/mutex
```

## 命令行分析

```bash
go tool pprof http://127.0.0.1:6060/debug/pprof/profile?seconds=30
go tool pprof http://127.0.0.1:6060/debug/pprof/heap
```

进入交互界面后常用命令：

```text
top
list FunctionName
web
peek FunctionName
```

## 排查思路

- CPU 高：先看 `top`，再用 `list` 定位热点函数具体行。
- 内存涨：比较不同时间点 heap profile，关注 `inuse_space` 和 `alloc_space`。
- goroutine 增长：查看 `goroutine` 堆栈是否集中卡在 channel、锁、I/O。
- 锁竞争：打开 mutex/block profile 后观察热点锁路径。

## 注意事项

- 生产环境不要把 pprof 暴露到公网。
- CPU profile 需要采样一段时间，时间太短容易失真。
- heap profile 默认更关注采样后的分配情况，不等同于完整内存快照。
- block/mutex profile 通常需要显式设置采样率。

## 关联

- [[sync_Pool原理]]
- [[Go GC]]
- [[性能优化]]
