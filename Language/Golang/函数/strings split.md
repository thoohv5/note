---
title: strings.split
date: 2026-04-07
tags: [编程语言, Golang, 函数]
type: note
status: incomplete
---

## strings.split

```go
strings.FieldsFunc(input, Split)
func Split(r rune) bool {
    return r == ':' || r == '.'
}
```