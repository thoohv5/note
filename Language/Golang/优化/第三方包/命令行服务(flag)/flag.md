---
title: flag
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
---

## flag

- 示例
    
    ```sql
    package main
    
    import (
    	"flag"
    	"fmt"
    	"os"
    )
    
    var s string
    var b bool
    
    // go run ./flag.go -s "str1" -b true
    // str1 string true bool [/Users/thooh/Library/Caches/go-build/2b/2bcf33ab4c766a814d6345071087cf675ddeaf10efee74f1cded0cce5e3a58ed-d/flag -s str1 -b true]
    
    // go run ./flag.go -s="str1" -b=true
    // str1 string true bool [/var/folders/sr/ygvxzfnn4nl3dr28cwxs46640000gn/T/go-build2655713578/b001/exe/flag -s=str1 -b=true]
    
    // go run ./flag.go -s="str1" -b
    // str1 string true bool [/var/folders/sr/ygvxzfnn4nl3dr28cwxs46640000gn/T/go-build2761976569/b001/exe/flag -s=str1 -b]
    
    func main() {
    
    	flag.StringVar(&s, "s", "str", "string type")
    	flag.BoolVar(&b, "b", false, "bool type")
    
    	flag.Parse()
    
    	fmt.Printf("%v %T %v %T %v\n", s, s, b, b, os.Args)
    
    }
    
    ```
    

### 参数格式

```go
-flag   // 只支持bool类型
-flag=x // 支持任何类型
-flag x // 只支持非bool类型，新版有所放宽
```