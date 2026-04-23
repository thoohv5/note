---
title: exec.Command
date: 2026-04-07
  - 编程语言
  - Golang
type: note
status: complete
---

## exec.Command

## 执行方式

### 同步

```go
## 同步
cmd := exec.Command("ls")
cmd.Run()
```

### 异步

```go
## 异步
cmd := exec.Command("ls")
cmd.Start()
cmd.Wait()
```

## `Kill`子进程

### 正常场景

```go
package main

import (
	"os"
	"os/exec"
	"time"
)

func main() {
	cmd := exec.Command("sleep", "5")
	cmd.Stdout = os.Stdout
	cmd.Stderr = os.Stderr
	if err := cmd.Start(); err != nil {
		panic(err)
	}
	time.Sleep(10 * time.Second)
	if err := cmd.Process.Kill(); err != nil {
		panic(err)
	}
	// if err := cmd.Wait(); err != nil {
	// 	panic(err)
	// }
}
```

### 进程fork出子进程

```bash
## scripts/script.sh
#!/bin/sh

/bin/sh "$PWD"/scripts/second.sh

## scripts/second.sh
#!/bin/sh

while true; do date >> /tmp/date.txt; sleep 1; done
```

```go
// main.go
package main

import (
	"fmt"
	"os"
	"os/exec"
	"time"
)

func main() {
	getwd, err := os.Getwd()
	if err != nil {
		panic(err)
	}
	cmd := exec.Command("/bin/sh", "-c", fmt.Sprintf("%s/scripts/script.sh", getwd)) // watch进程fork出了其他子进程
	cmd.Stdout = os.Stdout
	cmd.Stderr = os.Stderr
	if err := cmd.Start(); err != nil { // 运行该命令
		panic(err)
	}
	time.Sleep(10 * time.Second)
	if err := cmd.Process.Kill(); err != nil {
		panic(err)
	}
	if err := cmd.Wait(); err != nil {
		panic(err)
	}
}
```

### 问题

Go是使用kill(2)向sh进程的PID发了一个KILL信号，但没有发给watch进程，sh进程被kill之后，导致watch进程变成孤儿进程。

```bash
  501  4503     1   0  8:49PM ttys002    0:00.03 /bin/sh .../scripts/second.sh
```

### 解决办法

`kill(2)`不但支持向单个`PID`发送信号，还可以向进程组发信号，我们只要把sh进程及其所有子进程放到一个进程组里，就可以批量Kill了。关键是PGID的设置，默认情况下，子进程会把自己的PGID设置成与父进程相同，所以，我们只要设置了sh进程的PGID，所有子进程也就相应的有了PGID。

```go
package main

import (
	"fmt"
	"os"
	"os/exec"
	"syscall"
	"time"
)

func main() {
	getwd, err := os.Getwd()
	if err != nil {
		panic(err)
	}
	cmd := exec.Command("/bin/sh", "-c", fmt.Sprintf("%s/scripts/script.sh", getwd)) // watch进程fork出了其他子进程
	cmd.Stdout = os.Stdout
	cmd.Stderr = os.Stderr

	cmd.SysProcAttr = &syscall.SysProcAttr{}
	cmd.SysProcAttr.Setpgid = true

	if err := cmd.Start(); err != nil { // 运行该命令
		panic(err)
	}
	time.Sleep(10 * time.Second)
	if err := syscall.Kill(-cmd.Process.Pid, syscall.SIGKILL); err != nil {
		panic(err)
	}
	if err := cmd.Wait(); err != nil {
		panic(err)
	}
}
```

上面这种方式适合非`su - <user> command`执行命令的方式，否则杀死父进程后，子进程将被托管成为孤儿进程。

```go
sysuser, err := user.Lookup("user1") // 通过用户名来获取用户信息
if err != nil {
	fmt.Println(err)
}
uid, err := strconv.Atoi(sysuser.Uid) // 将UID的类型转换成 uint32
if err != nil {
	fmt.Println(err)
}
gid, err := strconv.Atoi(sysuser.Gid) // 将GID的类型转换成 uint32
if err != nil {
	fmt.Println(err)
}
cmd.SysProcAttr = &syscall.SysProcAttr{}
cmd.SysProcAttr.Credential = &syscall.Credential{
	Uid:         uint32(uid),
	Gid:         uint32(gid),
}
```