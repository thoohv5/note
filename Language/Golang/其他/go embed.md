---
title: go:embed
date: 2026-04-07
  - 编程语言
  - Golang
type: guide
status: incomplete
---

## go:embed

### 限制

1. 文件不是`utf8`编码时，输出内容为中文会乱码
2. 嵌入文件只能为源码文件同级目录和子目录下的文件