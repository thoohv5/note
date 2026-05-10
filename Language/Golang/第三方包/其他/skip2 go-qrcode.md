---
title: skip2/go-qrcode
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
---

## 概述

`github.com/skip2/go-qrcode` 是 Go 的 QR Code 编码库，支持多种纠错级别和输出格式。

```bash
go get github.com/skip2/go-qrcode
```

```go
import qrcode "github.com/skip2/go-qrcode"

// 生成 PNG 字节
png, err := qrcode.Encode("https//example.com", qrcode.Medium, 256)

// 写入文件
err = qrcode.WriteFile("https//example.com", qrcode.Medium, 256, "qr.png")
```

## 纠错级别

| 级别 | 恢复能力 | 适用场景 |
|------|----------|----------|
| Low (L) | ~7% | 理想显示环境 |
| Medium (M) | ~15% | 一般用途（推荐） |
| High (H) | ~25% | 带 logo 或可能受损 |
| Highest (Q) | ~30% | 恶劣环境 |

## 高级用法

- `qrcode.New(content, level)` 返回 QRCode 对象，可设定前景/背景色、禁用边框
- 支持 `WriteFile` 直接写 PNG 文件
- 支持 `Encode` 返回 `[]byte`

## 注意事项

- QR 码容量有限：数字 < 7089 字符，字母数字 < 4296，二进制 < 2953 字节
- 纠错级别越高，有效数据容量越小
- 微信/支付宝二维码需特定前缀协议

## 参考

- [github.com/skip2/go-qrcode](https://github.com/skip2/go-qrcode)
- [[Language/Golang]]