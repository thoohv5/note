---
title: 单例模式（single mode）
date: 2026-04-07
tags: [编程语言, Golang]
type: reference
status: complete
---

# 单例模式（single mode）

# 单例实现

### 懒汉模式，非线程安全

```go
package singleton

type singleton struct {
}

var instance *singleton

func GetInstance() *singleton {
	if instance == nil {
		instance = &singleton{}
	}
	return instance
}
```

### 线程锁

```go
package singleton

import (
	"sync"
)

type singleton struct {
}

var (
	mu       sync.Mutex
	instance *singleton
)

func GetInstance() *singleton {
	mu.Lock()
	defer mu.Unlock()
	if instance == nil {
		instance = &singleton{}
	}
	return instance
}
```

### 检查锁

“双重检查锁定”（`Double-Checked Locking`）是一种用于提高[多线程](https://so.csdn.net/so/search?q=多线)8B&spm=1001.2101.3001.7020环境下性能的设计模式，主要用于懒初始化（lazy initialization）场景。它确保了在多线程情况下，某个资源（如单例实例）只被初始化一次，并且在初始化后访问时无需加锁，从而减少不必要的锁开销。

**双重检查锁定的工作原理**

双重检查锁定的核心思想是，在对某个共享资源进行访问时，首先在锁外进行一次检查，如果不满足条件（例如资源尚未初始化），才在锁内进行第二次检查，并执行[初始化操作](https://so.csdn.net/so/search?q=初始化操)9C&spm=1001.2101.3001.7020。这种方式可以避免每次访问资源时都进行加锁操作，降低锁带来的性能开销。

```go
package singleton

import (
	"sync"
)

type singleton struct {
}

var (
	mu       sync.Mutex
	instance *singleton
)

func GetInstance() *singleton {
	if instance == nil {
		mu.Lock()
		defer mu.Unlock()
		if instance == nil {
			instance = &singleton{}
		}
	}
	return instance
}
```

### Once

```go
package singleton

import (
	"sync"
)

type singleton struct {
}

var (
	once     sync.Once
	instance *singleton
)

func GetInstance() *singleton {
	once.Do(func() {
		instance = &singleton{}
	})
	return instance
}
```

# 附录

[Go并发编程 - sync.Once 单实例模式的思考](https://mp.weixin.qq.com/s/ELnUQL-EnK8w8EpdbZKDWA)