# sync/errgroup

**errgroup 的优势**
与 sync.WaitGroup 相比，errgroup.Group 具有以下优势：
• **错误处理**：sync.WaitGroup 只负责等待 goroutine 完成，不处理返回值或错误。而 errgroup.Group 虽然不能直接处理返回值，但可以在某个 goroutine 遇到错误时立即取消其他正在运行的 goroutine，并在 Wait 方法中返回第一个非 nil 的错误。
• **Context 取消**：errgroup 可以与 context.Context 配合使用。当某个 goroutine 遇到错误时，可以自动取消其他 goroutine，有效控制资源，避免不必要的工作。
• **简化并发编程**：使用 errgroup 可以减少错误处理的样板代码。开发者不需要手动管理错误状态和同步逻辑，使并发编程更简单、更易维护。
• **限制并发数量**：errgroup 提供了限制并发 goroutine 数量的接口，以避免过载，这是 sync.WaitGroup 所不具备的功能。

# Context取消

```go
package main

import (
    "context"
    "fmt"
    "net/http"
    "sync"
    "golang.org/x/sync/errgroup"
)

func main() {
    var urls = []string{
        "http://www.golang.org/",
        "http://www.google.com/",
        "http://www.somestupidname.com/", 
    }

    g, ctx := errgroup.WithContext(context.Background())

    var result sync.Map

    for _, url := range urls {
        g.Go(func() error {
            req, err := http.NewRequestWithContext(ctx, "GET", url, nil)
            if err != nil {
                return err 
            }

            resp, err := http.DefaultClient.Do(req)
            if err != nil {
                return err 
            }
            defer resp.Body.Close()

            result.Store(url, resp.Status)
            return nil
        })
    }

    if err := g.Wait(); err != nil {
        fmt.Println("Error: ", err)
    }

    result.Range(func(key, value any) bool {
        fmt.Printf("fetch url %s status %s\n", key, value)
        returntrue
    })
}
```

# 限制并发数量

```go
package main

import (
    "fmt"
    "time"
    "golang.org/x/sync/errgroup"
)

func main() {
    var g errgroup.Group
    g.SetLimit(3)

    for i := 1; i <= 10; i++ {
        if g.TryGo(func() error {
            fmt.Printf("Goroutine %d is starting\n", i)
            time.Sleep(2 * time.Second) 
            fmt.Printf("Goroutine %d is done\n", i)
            return nil
        }) {
            fmt.Printf("Goroutine %d started successfully\n", i)
        } else {
            fmt.Printf("Goroutine %d could not start (limit reached)\n", i)
        }
    }

    if err := g.Wait(); err != nil {
        fmt.Printf("Encountered an error: %v\n", err)
    }

    fmt.Println("All goroutines complete.")
}
```