---
title: 漏桶（Leaky Bucket）
date: 2026-04-07
tags:
  - 微服务
  - 分布式
type: note
status: complete
---

### 漏桶（Leaky Bucket）

- 一个固定大小的水桶
- 以固定速率流出
- 水桶满了,则进行溢出(拒绝)

**优点**

- **解决了计数器限流算法的毛刺问题**
- 整体流量控制的比较平稳

**缺点**

- **无法应对某些突发的流量**

```go
package main

import (
	"fmt"  //用于格式化输入和输出
	"sync" //用于提供同步原语，如互斥锁（Mutex）
	"time" //用于处理时间相关的操作
)

// LeakyBucket 结构体定义了漏桶的主要属性
type LeakyBucket struct {
	capacity int           // 桶的容量
	water    int           // 当前桶中的水量
	leakRate int           // 漏水速率（每秒漏出的数量）
	mutex    sync.Mutex    // 互斥锁，用于并发访问保护
	ticker   *time.Ticker  // 定时器，用于定时漏水
	stopCh   chan struct{} // 停止通道，用于停止定时器
	errCh    chan error
}

// NewLeakyBucket 函数用于创建一个新的漏桶实例
func NewLeakyBucket(capacity, leakRate int) *LeakyBucket {
	return &LeakyBucket{
		capacity: capacity,
		leakRate: leakRate,
		stopCh:   make(chan struct{}),
		errCh:    make(chan error),
	}
}

// Start 方法用于启动漏桶的漏水过程
func (lb *LeakyBucket) Start() {
	lb.ticker = time.NewTicker(1 * time.Millisecond)
	go func() {
		defer func() {
			if rec := recover(); rec != nil {
				lb.Stop()
				lb.errCh <- fmt.Errorf("%v", rec)
			}
		}()
		for {
			select {
			case <-lb.ticker.C:
				lb.leak()
			case <-lb.stopCh:
				lb.ticker.Stop()
				return
			}
		}
	}()
	return
}

// Stop 方法用于停止漏桶的漏水过程
func (lb *LeakyBucket) Stop() {
	close(lb.stopCh)
}

// leak 方法用于从桶中漏出一定量的水
func (lb *LeakyBucket) leak() {
	lb.mutex.Lock()
	defer lb.mutex.Unlock()
	if lb.water > 0 {
		lb.water -= lb.leakRate
		if lb.water < 0 {
			lb.water = 0
		}
		//fmt.Println(lb.water)
		// 这里可以添加请求处理的逻辑
		time.Sleep(10 * time.Millisecond) // 模拟请求处理时间
	}
}

// Allow 方法用于尝试向桶中加入一定量的水，并返回是否成功
func (lb *LeakyBucket) Allow(amount int) bool {
	lb.mutex.Lock()
	defer lb.mutex.Unlock()
	if lb.water+amount <= lb.capacity {
		lb.water += amount
		return true
	}
	return false
}

func main() {
	// 创建一个容量为10，漏水速率为1的漏桶
	bucket := NewLeakyBucket(10, 1)
	go func() {
		fmt.Println(<-bucket.errCh)
	}()
	bucket.Start() // 启动漏桶

	// 模拟请求处理
	for i := 0; i < 2000; i++ {
		go func(reqID int) {
			if bucket.Allow(1) {
				fmt.Print(1)
			} else {
				fmt.Print(0)
			}
		}(i)
		time.Sleep(time.Duration(5) * time.Millisecond) // 模拟随机请求间隔
	}
	bucket.Stop() // 停止漏桶
}

```