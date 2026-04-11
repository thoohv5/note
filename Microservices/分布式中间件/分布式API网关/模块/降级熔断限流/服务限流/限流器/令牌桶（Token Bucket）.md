---
title: 令牌桶（Token Bucket）
date: 2026-04-07
tags: [微服务, 分布式]
type: note
status: complete
---

# 令牌桶（Token Bucket）

核心思想：

- 一个固定大小的桶用来存放token
- 以固定速率放入token
- 每次请求获取token则处理,否则拒绝

![](https://img2020.cnblogs.com/blog/1093617/202009/1093617-20200906170459047-184324938.png)

[令牌桶算法](https://zhida.zhihu.com/search?content_id=162744473&content_type=Article&match_order=1&q=令牌桶算)95&zhida_source=entity用来控制一段时间内发送到网络上的数据的数目，并允许突发数据的发送。

```go
package main

import (
    "fmt"

    "golang.org/x/time/rate"
)

func main() {

    //限速50qps, 桶大小100
    limit := rate.NewLimiter(50, 100)
    for i := 0; i < 1000; i++ {
        allow := limit.Allow()
        if allow {
            fmt.Printf("i=%d is allow\n", i)
            continue
        }
        fmt.Printf("i=%d is not allow\n", i)
    }
}
```

```go
package main

import (
	"fmt"
	"math/rand"
	_ "net/http/pprof"
	"sync"
	"time"
)

type TokenBucket struct {
	mux sync.Mutex
	max int
	num int
}

func NewTokenBucket(max int) *TokenBucket {
	return &TokenBucket{}
}

func (tb *TokenBucket) Get(num int) bool {
	defer tb.mux.Unlock()
	tb.mux.Lock()
	if tb.num <= 0 {
		return false
	}
	tb.num -= num
	return true
}

func (tb *TokenBucket) Set(num int) {
	defer tb.mux.Unlock()
	tb.mux.Lock()
	tb.num += num
}

func main() {
	tb := NewTokenBucket(10)

	closeCh := make(chan struct{})
	go func() {
		tr := time.NewTicker(100 * time.Millisecond)
		for {
			select {
			case <-tr.C:
				tb.Set(1)
			case <-closeCh:
				tr.Stop()
				return
			}
		}
	}()

	time.Sleep(2 * time.Second)
	for i := 0; i < 1000; i++ {
		go func() {
			time.Sleep(time.Duration(rand.Intn(10)) * time.Second)
			if tb.Get(rand.Intn(3)) {
				fmt.Print(1)
				time.Sleep(50 * time.Millisecond)
			} else {
				fmt.Print(0)
			}
		}()
	}

	select {}

}

```