---
title: array2slice
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: note
status: incomplete
---

## array2slice

在Go中，数组是相同数据类型组成长度固定的连续内存数据结构，slice只是指向底层数组的引用类型。

### 数组转切片

```go
package main

import "fmt"

func main() {
    s := []int{1, 2, 3}
    var a [3]int

    fmt.Println(copy(a[:2], s))
    fmt.Println(a)
}
```

### 切片转数组

```go
package main

import "fmt"

func main() {
    a := [...]int{1, 2, 3}
    s := make([]int, 3)

    fmt.Println(copy(s, a[:2]))
    fmt.Println(s)
}
```