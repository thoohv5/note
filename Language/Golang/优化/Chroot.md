---
title: Chroot
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
---

## Chroot

### **Chroot 带来的安全性问题**

![](https://pic4.zhimg.com/80/v2-23007c60e1fd6a0bdff18a3341ba01f3_720w.webp)

**图 2 ，chroot 示例**

Chroot 可以将进程及其子进程与操作系统的其余部分隔离开来。但是，**对于 root process** ，却**可以任意退出 chroot**。

```
package main

import (
 "log"
 "os"
 "syscall"
)

func getWd() (path string) {
 path, err := os.Getwd()
 if err != nil {
  log.Println(err)
 }
 log.Println(path)
 return
}

func main() {
 RealRoot, err := os.Open("/")
 defer RealRoot.Close()
 if err != nil {
  log.Fatalf("[ Error ] - /: %v\\n", err)
 }
 path := getWd()

 err = syscall.Chroot(path)
 if err != nil {
  log.Fatalf("[ Error ] - chroot: %v\\n", err)
 }
 getWd()

 err = RealRoot.Chdir()
 if err != nil {
  log.Fatalf("[ Error ] - chdir(): %v", err)
 }
 getWd()

 err = syscall.Chroot(".")
 if err != nil {
  log.Fatalf("[ Error ] - chroot back: %v", err)
 }
 getWd()
}

```

分别以普通用户和 sudo 的方式运行：

```
➜  chroot go run main.go
2021/11/18 00:46:21 /tmp/chroot
2021/11/18 00:46:21 [ Error ] - chroot: operation not permitted
exit status 1
➜  chroot sudo go run main.go
2021/11/18 00:46:25 /tmp/chroot
2021/11/18 00:46:25 /
2021/11/18 00:46:25 (unreachable)/
2021/11/18 00:46:25 /

```

可以看到如果是使用 `sudo`来运行的时候，程序在当前目录和系统原本的根目录下进行了切换。而普通用户则无权限操作。