---
title: shopspring/decimal
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
source: "https://github.com/shopspring/decimal"
---

## shopspring/decimal

> Go 原生 `float64` 不适用于精确货币计算。`shopspring/decimal` 提供任意精度十进制数类型，避免浮点误差。

### 核心特性

- 任意精度、固定精度的十进制运算（底层用 `big.Int` 系数 + 指数表示）
- 支持算术 / 比较 / 取整 / 格式化
- JSON / SQL 序列化内置支持

### 使用示例

```go
import "github.com/shopspring/decimal"

price, _ := decimal.NewFromString("19.99")
qty := decimal.NewFromInt(3)
taxRate := decimal.NewFromFloat(0.08)

subtotal := price.Mul(qty)          // 59.97
tax := subtotal.Mul(taxRate)        // 4.7976
total := subtotal.Add(tax).Round(2) // 64.77
```

### 注意事项

- `NewFromFloat` 会把 `0.1` 转为 `0.1000000000000000055511151...`，推荐 `NewFromString`
- 除法需指定精度或模式，否则 panic
- 不可比较（`decimal.Decimal` 不是 `comparable`），需用 `Equal()` 方法
- MySQL `DECIMAL` 映射建议配合 GORM `decimal.Decimal` scanner 使用

### 参考

- [GitHub: shopspring/decimal](https://github.com/shopspring/decimal)
- 相关：[[浮点数陷阱]]