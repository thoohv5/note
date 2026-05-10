---
title: 滑动窗口（Sliding Window）
date: 2026-04-07
tags: [微服务, 分布式, API网关]
type: reference
status: complete
---

### 滑动窗口（Sliding Window）

1.将时间划分为细粒度的区间，每个区间维持一个计数器，每进入一个请求则将计数器加一。
2,多个区间组成一个时间窗口，每流逝一个区间时间后，则抛弃最老的一个区间，纳入新区间
3.若当前窗口的区间计数器总和超过设定的限制数量，则本窗口内的后续请求都被丢弃。

```go
package main

import (
	"fmt"  //用于格式化输入和输出
	"sync" //用于提供同步原语，如互斥锁（Mutex）
	"time" //用于处理时间相关的操作
)

type SlidingWindowLimiter struct {
	limit         int           //允许的最大请求数
	windowSize    time.Duration //时间窗口的大小
	count         int           //当前时间窗口内的请求数
	lastResetTime time.Time     //上次重置计数器的时间
	mu            sync.Mutex    //互斥锁，用于保护共享资源的并发访问
	sharding      int
}

// NewSlidingWindowLimiter 创建并返回一个新的 SlidingWindowLimiter 实例
// 初始化 limit 和 windowSize
func NewSlidingWindowLimiter(limit int, sharding int, windowSize time.Duration) *SlidingWindowLimiter {
	return &SlidingWindowLimiter{
		limit:      limit,
		windowSize: windowSize,
		count:      0,
		sharding:   sharding,
	}
}

// Allow 获取当前时间，并移除过期的时间戳
// 如果当前时间戳的数量小于 limit，则将当前时间戳添加到切片中，并返回 true，表示请求被允许
// 否则返回 false，表示请求被拒绝
func (swl *SlidingWindowLimiter) Allow() bool {
	swl.mu.Lock()
	defer swl.mu.Unlock()

	now := time.Now()
	if now.Sub(swl.lastResetTime) >= swl.windowSize {
		swl.count = 0
		swl.lastResetTime = now
	}

	if swl.count < swl.limit/swl.sharding {
		swl.count++
		return true
	}
	return false
}

// 创建一个每秒最多允许 100 个请求的限流器
// 循环 2000 次，每次尝试发送请求，并根据 Allow 方法的返回值打印相应的结果
// 每次循环之间暂停 5 毫秒
func main() {
	limiter := NewSlidingWindowLimiter(100, 10, 80*time.Millisecond)

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