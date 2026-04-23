---
title: Go deadlock
date: 2026-04-07
  - 编程语言
  - Golang
type: note
status: complete
---

## Go deadlock

```bash
func main() {
    count := 10
    ch := generate(count)
    for v := range ch { // for i 不会造成死锁
        fmt.Println(v)
    }
｝

func generate(count int) <-chan int {
    ch := make(chan int)
    go func() {
        for i := 0; i < count; i++ {
            ch <- i
        }
    }()
    return ch
}
```

### 问题

> fatal error: all goroutines are asleep - deadlock!
> 

### 分析

```bash
for range是阻塞式读取channel，只有channel close之后才会结束

只要没有goroutine持有channel，相关资源会自动释放， channel不需要通过close释放资源。
```