---
title: Context
date: 2026-04-07
  - 编程语言
  - Golang
type: note
status: complete
---

## Context

## 实现

mutex与channel

## 使用

```bash
func main() {
    messages := make(chan int, 10)

    // producer
    for i := 0; i < 10; i++ {
        messages <- i
    }

    ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)

    // consumer
    go func(ctx context.Context) {
        ticker := time.NewTicker(1 * time.Second)
        for _ = range ticker.C {
            select {
            case <-ctx.Done():
                fmt.Println("child process interrupt...")
                return
            default:
                fmt.Printf("send message: %d\n", <-messages)
            }
        }
    }(ctx)

    defer close(messages)
    defer cancel()

    select {
    case <-ctx.Done():
        time.Sleep(1 * time.Second)
        fmt.Println("main process exit!")
    }
}
```

```go
func work(ctx context.Context, i int, ch chan<- struct{}) {
	select {
	case <-ctx.Done():
		fmt.Println("cancel", i)
		return
	case ch <- func(i int) struct{} {
		defer func() {
			fmt.Println("work done", i)
		}()
		time.Sleep(time.Second)
		fmt.Println("work", i)
		return struct{}{}
	}(i):
		fmt.Println("do", i)
		return
	}
}
```

<aside>
💡 sync/errgroup

</aside>