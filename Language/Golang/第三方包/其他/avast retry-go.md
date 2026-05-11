---
title: avast/retry-go
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
source: "https://github.com/avast/retry-go"
---

## avast/retry-go

Go 重试库，2.9k+ stars，MIT 协议，API 简洁。

### 安装

```bash
go get github.com/avast/retry-go/v4
```

### 基础用法

```go
err := retry.Do(
    func() error {
        resp, err := http.Get(url)
        if err != nil {
            return err
        }
        defer resp.Body.Close()
        // ...
        return nil
    },
    retry.Attempts(5),
    retry.Delay(100*time.Millisecond),
)
```

### 常用选项

| 选项 | 说明 |
|------|------|
| `Attempts(n)` | 重试次数，`0` 表示无限重试 |
| `Delay(d)` | 重试间隔 |
| `MaxDelay(d)` | 退避最大延迟 |
| `MaxJitter(d)` | 随机抖动上限 |
| `DelayType(func)` | 自定义延迟策略 |
| `OnRetry(func)` | 每次重试回调 |
| `RetryIf(func)` | 按错误类型决定是否重试 |
| `Context(ctx)` | 绑定 context 取消 |

### 延迟策略

- `FixedDelay` — 固定延迟
- `BackOffDelay` — 指数退避
- `RandomDelay` — 随机延迟
- `CombineDelay` — 组合多个延迟策略

### 不可重试错误

```go
retry.Do(
    func() error {
        return retry.Unrecoverable(errors.New("参数错误"))
    },
    retry.Attempts(3),
)
```

### 带返回值重试（v5+）

```go
body, err := retry.DoWithData(
    func() ([]byte, error) { return ioutil.ReadAll(resp.Body) },
    retry.Attempts(3),
)
```

### 注意事项

- 幂等操作适用，非幂等操作用 `RetryIf` 过滤
- 生产环境建议配合熔断器使用

### 相关笔记

- [[重试机制]]
- [[熔断器]]