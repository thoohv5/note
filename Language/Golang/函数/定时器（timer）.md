---
title: 定时器（timer）
date: 2026-04-07
tags: [编程语言, Golang, 函数]
type: note
status: complete
---
## 定时器（timer）

### 常见用法
```go
t := time.NewTimer(2 * time.Second)
<-t.C
```

### time.After 的内存泄漏陷阱

```go
// ❌ 错误：每次循环创建新 Timer
for {
    select {
    case <-time.After(5 * time.Second):
        doWork()
    }
}
```

原因：`time.After` 创建的 Timer 不会被 GC 回收，直到超时触发。在循环中使用会导致大量 Timer 积压在 runtime 的 timer heap 中，造成内存泄漏。

```go
// ✅ 正确：复用 Timer
t := time.NewTimer(5 * time.Second)
for {
    t.Reset(5 * time.Second)
    select {
    case <-t.C:
        doWork()
    }
}
```

### Timer vs Ticker
- `Timer`: 单次触发
- `Ticker`: 循环触发
- `time.AfterFunc`: 延迟执行函数

### 参考
- [Go 内存泄露之痛，time.After 问题根因](https://mp.weixin.qq.com/s/KSBdPkkvonSES9Z9iggElg)
- [Go timer 调度](https://mp.weixin.qq.com/s/iseiQ20eIUR9i02fy1tFhg)
- [[协程（goroutine）]]
