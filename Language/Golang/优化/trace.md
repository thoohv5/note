---
title: trace
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: incomplete
---

## trace

```bash
package main

import (
    "os"
    "fmt"
    "runtime/trace"
)

func main() {

    //创建trace文件
    f, err := os.Create("trace.out")
    if err != nil {
        panic(err)
    }

    defer f.Close()

    //启动trace goroutine
    err = trace.Start(f)
    if err != nil {
        panic(err)
    }
    defer trace.Stop()

    //main
    fmt.Println("Hello World")
}
```

```bash
go tool trace trace.out
```