---
title: Uber automaxprocs
date: 2026-04-07
tags: [编程语言, Golang]
type: note
status: incomplete
---

# Uber automaxprocs

```bash
package main

import (
    "fmt"
    "runtime"

    _ "go.uber.org/automaxprocs"
)

func main() {
    fmt.Printf("GOMAXPROCS = %d\n", runtime.GOMAXPROCS(0))
}
```