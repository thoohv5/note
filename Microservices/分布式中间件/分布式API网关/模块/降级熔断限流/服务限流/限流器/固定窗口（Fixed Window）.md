---
title: 固定窗口（Fixed Window）
date: 2026-04-07
  - 微服务
  - 分布式
type: reference
status: complete
---

### 固定窗口（Fixed Window）

- 在单位时间内进行计数,如果大于设置的最大值,则进行拒绝
- 如果过了单位时间,则重新进行计数

![](https://img2020.cnblogs.com/blog/1093617/202009/1093617-20200906164032578-410838134.png)

[https://img2020.cnblogs.com/blog/1093617/202009/1093617-20200906164032578-410838134.png](https://img2020.cnblogs.com/blog/1093617/202009/1093617-20200906164032578-410838134.png)

**优点：**

实现非常简单

**缺点：**

突发流量会出现毛刺现象

比如一秒限流100个请求, 前100ms内处理完了100个请求,后900ms时间内没有请求处理

计数不准确

![](https://img2020.cnblogs.com/blog/1093617/202009/1093617-20200906164224687-458059687.png)

[https://img2020.cnblogs.com/blog/1093617/202009/1093617-20200906164224687-458059687.png](https://img2020.cnblogs.com/blog/1093617/202009/1093617-20200906164224687-458059687.png)

```go
package main

import (
	"fmt"  //用于格式化输入和输出
	"sync" //用于提供同步原语，如互斥锁（Mutex）
	"time" //用于处理时间相关的操作
)

type FixedWindowLimiter struct {
	limit         int           //允许的最大请求数
	windowSize    time.Duration //时间窗口的大小
	count         int           //当前时间窗口内的请求数
	lastResetTime time.Time     //上次重置计数器的时间
	mu            sync.Mutex    //互斥锁，用于保护共享资源的并发访问
}

// NewFixedWindowLimiter 创建并返回一个新的 FixedWindowLimiter 实例
// 初始化 limit、windowSize 和 lastResetTime
func NewFixedWindowLimiter(limit int, windowSize time.Duration) *FixedWindowLimiter {
	return &FixedWindowLimiter{
		limit:         limit,
		windowSize:    windowSize,
		lastResetTime: time.Now(),
	}
}

// Allow 检查当前时间是否已经超过了时间窗口的大小
// 如果超过了时间窗口，则重置计数器并将 lastResetTime 更新为当前时间
// 如果当前时间窗口内的请求数小于 limit，则增加计数器并返回 true，表示请求被允许
// 否则返回 false，表示请求被拒绝
func (fwl *FixedWindowLimiter) Allow() bool {
	fwl.mu.Lock()
	defer fwl.mu.Unlock()

	now := time.Now()
	if now.Sub(fwl.lastResetTime) >= fwl.windowSize {
		fwl.count = 0
		fwl.lastResetTime = now
	}

	if fwl.count < fwl.limit {
		fwl.count++
		return true
	}
	return false
}

// 创建一个每秒最多允许 100 个请求的限流器
// 循环 2000 次，每次尝试发送请求，并根据 Allow 方法的返回值打印相应的结果
// 每次循环之间暂停 5 毫秒
func main() {
	limiter := NewFixedWindowLimiter(100, time.Second)

	for i := 0; i < 2000; i++ {
		if limiter.Allow() {
			fmt.Print(1)
		} else {
			fmt.Print(0)
		}
		time.Sleep(5 * time.Millisecond)
	}
}

```