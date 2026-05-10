---
title: golang编译参数ldflags子路径不生效
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
---

## golang编译参数ldflags子路径不生效

标签: Golang
URL: https://segmentfault.com/a/1190000041335961
状态: 待完成
类型: 文字

![](https://static.segmentfault.com/main_site_next/34665dec/touch-icon.png)

---

`golang` 在编译时，通过加入`- ldflags` 的参数可以替换编译的二进制包中的文件内容。例如替换版本号，构建日期，Git commit等信息时很方便。

例如替换main包中的变量如下：

```
package main

var version="N/A"

func main() {
    fmt.Println(version)
}
```

编译时加上参数：`go build -a -ldflags "-X main.version=1.2" -o bin/demo main.go`

运行测试如下：

```
~ ./bin/demo
1.2
```

但是测试到覆盖子包下的变量时，发现一直替换不成功。

```
~ mkdir pkg
~ touch pkg/vars.go
~ cat pkg.vars.go
package pkg

import "fmt"

var version="N/A"

func Init()  {
    fmt.Printf("pkg.version: %s\n",version)
}
```

在main函数中调用Init

```
func main() {
    fmt.Println(version)
    pkg.Init()
}
```

编译测试：

```
go build -a -ldflags "-X main.version=1.2 -X pkg/pkg.version=1.3" -o bin/demo main.go
./bin/demo
1.2
pkg.version: N/A
```

如上发现测试并不生效，网上有些说法是要相对GOPATH下去写，要强制带上工程名，例如：`demo/pkg/pkg.version`，这种是可以生效的，但是如果工程路径不止一级，例如`github.com/csi/demo`，这样的全路径加上去也是不行的。

究竟这个路径到底是什么，根据网上的资料检索，发现有一个工具可以直接列出编译的二进制文件中的变量路径：

```
go tool nm ./bin/demo | grep version
 162f6e0 D demo/pkg.version
 162f870 D main.version
 1404b10 R main.version.str
```

通过这个工具，可以直观得到子包中的变量全路径到底是什么。如果是多级子目录的话，测试如下：

```
cd github.com/kubernetes-csi/csi-driver-nfs
go tool nm ./bin/amd64/nfsplugin| grep driverVersion
 18a3600 D csi-driver-nfs/pkg/nfs.driverVersion
 155b4c8 R csi-driver-nfs/pkg/nfs.driverVersion.str
```

发现其实只需要写一级目录就可以了，如果实际build过程中，还是有问题，那么可以使用`go tool nm`工具查看路径即可解决问题。