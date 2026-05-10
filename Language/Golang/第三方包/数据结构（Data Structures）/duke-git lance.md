---
title: duke-git/lancet
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: reference
status: complete
source: https://github.com/duke-git/lancet
---

## lancet

lancet 是一个全面、高效的 Go 工具函数库，灵感来自 Java 的 `apache/common` 和 `hutool`，提供 600+ 常用函数。

### 安装

```bash
go get github.com/duke-git/lancet/v2
```

### 常用模块

- **strutil**：字符串操作（反转、驼峰/蛇形转换、截断）
- **slice**：切片操作（去重、差集、并集、分块）
- **datetime**：时间日期处理（格式化、计算差、时区）
- **mathutil**：数学工具（最大/最小、随机数）
- **retry**：重试机制（指数退避、最大次数）
- **cryptor**：加密工具（AES、RSA、MD5、SHA）
- **fileutil**：文件操作（读写、遍历、类型判断）
- **validator**：数据校验（邮箱、URL、身份证）

### 基本示例

```go
import "github.com/duke-git/lancet/v2/slice"

// 去重
slice.Unique([]int{1, 2, 2, 3}) // [1, 2, 3]

// 分块
slice.Chunk([]int{1, 2, 3, 4, 5}, 2) // [[1,2] [3,4] [5]]

import "github.com/duke-git/lancet/v2/strutil"

// 驼峰转蛇形
strutil.CamelToSnake("helloWorld") // "hello_world"
```

### 注意

- v2 版本包路径不同，注意导入路径。
- 函数均为纯函数，不修改原数据。

### 相关

- [[Go 常用工具库]]