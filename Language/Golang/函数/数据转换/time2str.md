---
title: time2str
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: note
status: incomplete
---

## time2str

### `time.Parse`

```go
In the absence of a time zone indicator, Parse returns a time in UTC.
```

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	fmt.Println(time.Now().Unix())
	fmt.Println(time.Now().Format(time.DateTime))

	t, err := time.Parse(time.DateTime, "2016-07-25 11:45:26")
	if err != nil {
		fmt.Println(err)
	}
	fmt.Println(t)
}

```