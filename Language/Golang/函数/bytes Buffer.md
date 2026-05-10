---
title: bytes.Buffer
date: 2026-04-07
tags: [编程语言, Golang, 函数]
type: reference
status: complete
source: https://mp.weixin.qq.com/s/BJ1fu9AwE2w7Ovg2C8pVbw
---

## bytes.Buffer

`bytes.Buffer` 是 Go 标准库中高效的字节缓冲区，实现 `io.Reader`、`io.Writer` 等多个接口，适合内存中读写字节数据。

### 常用操作

```go
var buf bytes.Buffer

// 写入
buf.WriteString("hello ")           // 写字符串
buf.Write([]byte("world"))          // 写字节切片
fmt.Fprintf(&buf, " %d", 42)       // 格式化写入

// 读取
data := buf.Bytes()                 // 获取全部字节（不消费）
str := buf.String()                 // 转为字符串（不消费）
line, _ := buf.ReadBytes('\n')      // 读取到分隔符

// 重置
buf.Reset()                         // 清空缓冲区
```

### 性能要点

- **预分配**：`buf.Grow(n)` 提前分配容量，避免多次扩容。
- **复用**：通过 `sync.Pool` 缓存 Buffer 实例减少 GC 压力。
- **零拷贝**：`Bytes()` 和 `String()` 直接返回底层切片引用，无需复制。

### 典型场景

- 字符串拼接（比 `+` 和 `Sprintf` 高效）
- HTTP 响应体构造
- 序列化/反序列化缓冲
- gRPC 消息编解码

### 并发安全

`bytes.Buffer` **不是并发安全的**，多 goroutine 访问需加锁。

### 相关

- [[sync Pool]] [[strings Builder]] [[IO Reader Writer]]