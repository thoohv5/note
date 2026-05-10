---
title: Go json/v2
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
source: https://mp.weixin.qq.com/s/pYlGeF1yBlIy07DDvsIzrw
---

## Go json/v2

Go 1.25 以 `GOEXPERIMENT=jsonv2` 形式引入全新的 `encoding/json/v2` 和底层包 `encoding/json/jsontext`，解决 v1 长期存在的内存瓶颈。

### v1 的"伪流"问题

`json.Encoder/Decoder` 提供流式 API 但内部全量缓冲：编码时先在内存构建完整 JSON 字符串，解码时先读入整个 JSON 值。处理百万级数据时内存分配约 **8 MiB**，与数据规模成线性 O(N) 关系。

### v2 的真流式处理

语法和语义分离，底层 `jsontext.Encoder/Decoder` 逐个 token 处理。

**流式编码**：

```go
enc := jsontext.NewEncoder(out)
enc.WriteToken(jsontext.BeginArray)       // 写入 [
for i := 0; i < numRecords; i++ {
    json.MarshalEncode(enc, record)       // 逐个编码
}
enc.WriteToken(jsontext.EndArray)         // 写入 ]
```

**流式解码**：

```go
dec := jsontext.NewDecoder(in)
dec.ReadToken()                            // 读取 [
for dec.PeekKind() != ']' {
    json.UnmarshalDecode(dec, &record)     // 逐个解码
}
```

### 内存对比

| 操作 | v1 (伪流) | v2 (真流) |
|------|-----------|-----------|
| Marshal 100万元素 | ~8 MiB | ~15 KiB |
| Unmarshal 100万元素 | ~8 MiB | ~15 KiB |

v2 将内存复杂度从 O(N) 降到 O(1)，固定 I/O 缓冲区开销。

### 性能提升

根据提案基准测试，v2 解码比 v1 快 **2.7～10.2 倍**。

### 使用方式

```bash
GOEXPERIMENT=jsonv2 go run main.go
```

### 注意事项

- v2 目前为实验特性，API 可能在正式版中调整
- `jsontext` 提供底层 token 级操作，日常使用仍推荐 `json.Marshal/Unmarshal`
- 大规模数据场景优先使用 `MarshalEncode/UnmarshalDecode`

### 相关笔记

- [[JSON时间格式化]]
- [[Go for range]]

> 参考：[Go json/v2 真流式处理](https://mp.weixin.qq.com/s/pYlGeF1yBlIy07DDvsIzrw)