---
title: exec.Command
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: guide
status: complete
---

## exec.Command

```go
cmd := exec.Command("ls", "-lh", "/usr/local/logs/*.log")
var stdout, stderr bytes.Buffer
cmd.Stdout = &stdout  // 标准输出
cmd.Stderr = &stderr  // 标准错误
err := cmd.Run()
outStr, errStr := string(stdout.Bytes()), string(stderr.Bytes())
```

## 执行带管道的命令

```sql
ps aux|grep go
```

### 使用 `sh -c ""`命令推荐做法

```sql
exec.Command("bash", "-c", "ps aux|grep go")
```

### 使用`io.Pipe()`连接两个命令

```sql
ps := exec.Command("ps", "aux")
grep := exec.Command("grep", "go")

r, w := io.Pipe() // 创建一个管道
defer r.Close()
defer w.Close()
ps.Stdout = w  // ps向管道的一端写
grep.Stdin = r // grep从管道的一端读

var buffer bytes.Buffer
grep.Stdout = &buffer

ps.Start()
grep.Start()

ps.Wait()
w.Close()
grep.Wait()

io.Copy(os.Stdout, &buffer)
```