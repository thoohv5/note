---
title: bluele/gcache
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
source: "https://github.com/bluele/gcache"
---

## bluele/gcache

Go 内存缓存库，支持多种淘汰策略，协程安全。

### 安装

```bash
go get github.com/bluele/gcache
```

### 淘汰策略

| 策略 | 说明 |
|------|------|
| `SimpleCache` | 默认，无淘汰优先级，依赖 map 迭代顺序 |
| `LRU` | 最近最少使用 |
| `LFU` | 最少使用频率 |
| `ARC` | 自适应替换，在 LRU/LFU 间动态平衡 |

### 基础用法

```go
gc := gcache.New(20).LRU().Build()
gc.Set("key", "ok")
value, err := gc.Get("key")
```

### 带过期时间

```go
gc.SetWithExpire("key", "ok", time.Second*10)
// 10 秒后 Get 返回 ErrKeyNotFound
```

### 自动加载（Cache-Aside）

```go
gc := gcache.New(20).LRU().
    LoaderFunc(func(key interface{}) (interface{}, error) {
        return loadFromDB(key), nil
    }).Build()
value, _ := gc.Get("key")  // 无缓存时自动调用 LoaderFunc
```

支持 `LoaderExpireFunc` 返回带过期时间的自动加载值。

### 事件回调

- `EvictedFunc` — 淘汰回调
- `AddedFunc` — 新增回调
- `PurgeVisitorFunc` — Purge 回调

### 注意事项

- 纯内存缓存，进程重启数据丢失
- `LoaderFunc` 配合 singleflight 防止缓存击穿
- 适合单机进程内缓存，不适合分布式场景

### 相关笔记

- [[go-cache]]
- [[Redis]]