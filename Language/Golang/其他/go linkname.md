---
title: go:linkname
date: 2026-04-07
tags: [编程语言, Golang, 其他]
type: reference
status: incomplete
---

## go:linkname

## 概述

`//go:linkname` 是 Go 语言中的一个编译器指令，用于在编译阶段将当前包内的函数或变量与另一个包中函数或变量（即使是未导出的）进行链接。

### 语法

```bash
//go:linkname localname [importpath.name]
```

## 附录

[mp.weixin.qq.com](https://mp.weixin.qq.com/s/nzbuLHfS4Nu2qtcd2bO6-w)