---
title: CGO编程
date: 2026-04-07
  - 编程语言
  - Golang
type: note
status: incomplete
---

## CGO编程

```go
/*
static int div(int a, int b) {
    return a/b;
}
*/
import "C"
import "fmt"

func main() {
    v := C.div(6, 3)
    fmt.Println(v)
}

```

## 附录

[第2章 CGO编程](https://chai2010.cn/advanced-go-programming-book/ch2-cgo/readme.html)