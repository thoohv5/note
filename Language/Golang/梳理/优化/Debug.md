# Debug

```bash
package main

import (
    "fmt"
    "time"
)

func main() {
    for i := 0; i < 5; i++ {
        time.Sleep(time.Second)
        fmt.Println("Hello World")
    }
}
```

```bash
GODEBUG=schedtrace=1000 ./trace2(可执行)
```