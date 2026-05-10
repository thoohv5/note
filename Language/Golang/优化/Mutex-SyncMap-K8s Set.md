---
title: Mutex & SyncMap & K8s Set
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: note
status: complete
---

## Mutex & SyncMap & K8s Set

```go
package main

import (
	"k8s.io/utils/set"
	"sync"
	"testing"
)

const (
	MaxNumber = 1000000
)

func BenchmarkMutexMapWrite(b *testing.B) {
	var mu sync.Mutex
	m := make(map[int]int)

	// b.ResetTimer() 防止初始化阶段的时间干扰测试结果
	// b.ResetTimer() 之后的代码才会被 go test -bench 计时
	b.ResetTimer()
	for i := 0; i < b.N; i++ {
		// 添加锁
		mu.Lock()
		m[i] = i
		// 解锁
		mu.Unlock()
	}
}

func BenchmarkMutexMapRead(b *testing.B) {
	var mu sync.Mutex
	m := make(map[int]int)

	for i := 0; i < MaxNumber; i++ {
		m[i] = i
	}
	b.ResetTimer()
	for i := 0; i < b.N; i++ {
		mu.Lock()
		// 通过 i % MaxNumber，让 key 限定在 0 ~ 999999 的范围，确保每次读取的 key 是已有数据
		// 确保每次读取的 key 是已有数据
		_ = m[i%MaxNumber]
		mu.Unlock()
	}
}

func BenchmarkSyncMapWrite(b *testing.B) {
	var sm sync.Map
	b.ResetTimer()
	for i := 0; i < b.N; i++ {
		sm.Store(i, i)
	}
}

func BenchmarkSyncMapRead(b *testing.B) {
	var sm sync.Map

	for i := 0; i < MaxNumber; i++ {
		sm.Store(i, i)
	}
	b.ResetTimer()
	for i := 0; i < b.N; i++ {
		_, _ = sm.Load(i % MaxNumber)
	}
}

func BenchmarkK8sSetWrite(b *testing.B) {
	s := set.New[int]()

	b.ResetTimer()
	for i := 0; i < MaxNumber; i++ {
		s.Insert(i)
	}
}

func BenchmarkK8sSetRead(b *testing.B) {
	s := set.New[int]()
	for i := 0; i < MaxNumber; i++ {
		s.Insert(i)
	}
	b.ResetTimer()
	for i := 0; i < b.N; i++ {
		_ = s.Has(i % MaxNumber)
	}
}

```

```go
go test -bench . -benchmem -benchtime=5s -count=3
```

```bash
goos: darwin
goarch: amd64
pkg: 2025-03-12/main
cpu: Intel(R) Core(TM) i7-8559U CPU @ 2.70GHz
																											平均时间（ns/op）         每次操作内存（B/op）分配次数
BenchmarkMutexMapWrite-8        36947913               304.4 ns/op            76 B/op          0 allocs/op
BenchmarkMutexMapWrite-8        42696808               270.7 ns/op            66 B/op          0 allocs/op
BenchmarkMutexMapWrite-8        40924384               257.5 ns/op            69 B/op          0 allocs/op
BenchmarkMutexMapRead-8         86665053                79.04 ns/op            0 B/op          0 allocs/op
BenchmarkMutexMapRead-8         89720310                73.70 ns/op            0 B/op          0 allocs/op
BenchmarkMutexMapRead-8         90058485                73.17 ns/op            0 B/op          0 allocs/op
BenchmarkSyncMapWrite-8          9773570               835.3 ns/op           157 B/op          5 allocs/op
BenchmarkSyncMapWrite-8          9386484               860.9 ns/op           161 B/op          5 allocs/op
BenchmarkSyncMapWrite-8          9001140               886.6 ns/op           166 B/op          5 allocs/op
BenchmarkSyncMapRead-8          43815096               127.9 ns/op             0 B/op          0 allocs/op
BenchmarkSyncMapRead-8          42844692               121.8 ns/op             0 B/op          0 allocs/op
BenchmarkSyncMapRead-8          43793593               128.6 ns/op             0 B/op          0 allocs/op
BenchmarkK8sSetWrite-8          1000000000               0.1044 ns/op          0 B/op          0 allocs/op
BenchmarkK8sSetWrite-8          1000000000               0.1008 ns/op          0 B/op          0 allocs/op
BenchmarkK8sSetWrite-8          1000000000               0.1014 ns/op          0 B/op          0 allocs/op
BenchmarkK8sSetRead-8           100000000               52.18 ns/op            0 B/op          0 allocs/op
BenchmarkK8sSetRead-8           100000000               50.85 ns/op            0 B/op          0 allocs/op
BenchmarkK8sSetRead-8           127083604               50.71 ns/op            0 B/op          0 allocs/op
```

|  | 写入速度（ns/op） | 读取数据（ns/op） | 特点 |
| --- | --- | --- | --- |
| `Mutex + map` | 157.2 | 39.5 | 读写性能均衡，适合中等读写场景 |
| `sync.Map` | 523.6 | 76.0 | 读多写少场景，写性能较差，内存分配较多 |
| `k8s.io/utils/set` | 0.07 | 29.7 | 超快读写性能，适合高并发、高性能场景 |