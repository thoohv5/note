---
title: dlv
date: 2026-04-07
  - 编程语言
  - Golang
type: guide
status: complete
---

## dlv

### 安装

```bash
go install github.com/go-delve/delve/cmd/dlv@latest
```

### 验证

```bash
dlv version
```

### 编译

```

在>= 1.10的 golang 版本中，设置-gcflags="all=-N -l"
在<1.10版本的 golang 中，设置-gcflags="-N -l"

其中:
-N表示不优化代码，并生成调试信息。
-l表示禁用函数内联优化

go build -gcflags="all=-N -l＂ -o main .

```

编译时不指定参数：dlv 调试，p打印变量时无法正常打印，报错：Command failed: could not find symbol value for 变量

### Panic时自动生成core与调试

GO提供环境变量GOTRACEBACK，控制在panic时的行为。

增加系统变量 GOTRACEBACK(设置值为[crash](https://so.csdn.net/so/search?q=crash&spm=1001.2101.3001.7020))

- GOTRACEBACK=none 只输出panic异常信息
- GOTRACEBACK=single 只输出被认为引发panic异常的那个goroutine的相关信息
- GOTRACEBACK=all 输出所有goroutines的相关信息，除去与go runtime相关的stack frames.
- GOTRACEBACK=system 输出所有goroutines的相关信息，包括与go runtime相关的stack frames,从而得知哪些goroutine是go runtime启动运行的
- GOTRACEBACK=crash 与system的唯一区别是crash会生成core[dump文件](https://so.csdn.net/so/search?q=dump文)B6&spm=1001.2101.3001.7020

### 常用命令

### 启动调试

### 基于进程

```bash
docker run -it --rm --cap-add=SYS_PTRACE --security-opt seccomp=unconfined \
    --name trade-debug trade-server-rocky

dlv attach 4445 --listen=:2345 --headless=true --api-version=2 --accept-multiclient

```

### 基于可执行文件

```bash
dlv --check-go-version=false --listen=:2345 --headless=true --api-version=2 --accept-multiclient exec /trade-server -- -conf /configs
```

### 基于源码文件

```bash
dlv debug .
run
```

### 调试指令

### 断点管理

断点的增删改查

| 指令 | 缩写 | 作用 |
| --- | --- | --- |
| **break** | b | 添加断点 |
| **breakpoints** | bp | 用于查看设置的所有断点，每个断点都有一个编号 |
| **clear** |  | 如clear 1，表示删除编号为 1 的断点 |
| **clearall** |  | 删除所有断点 |
| toggle |  | 用于启用/禁用断点。如toggle 1 |
| condition | cond | 用于设置条件断点，如cond 2 i == 10指定断点 2 在 i等于 10 时执行 |

需要注意的是，使用 `break` 创建断点时，有几种方法:

- `b 包名.方法名`: 在指定包的函数中设置断点，如`b main.main`。如果函数名全局唯一，则不用指定包名
- `b 文件名:行数`: 在指定的 go 文件的指定行设置断点，如`b main.go:14`

### 调试执行

控制程序的执行

| 指令 | 缩写 | 作用 |
| --- | --- | --- |
| **next** | n | 执行到下一行，如果是函数，不会进入函数 |
| **continue** | c | 执行到下一个断点处或结束执行 |
| **step** | s | 执行到下一步，如果是函数，会进入函数内部 |
| **stepout** | so | 跳出当前函数 |
| restart | r | 重新执行程序，断点会保留。注意无法用于 attach 的进程 |
| step-instruction | si | 执行到下一行机器码，一般在查看汇编代码时使用 |
| rebuild |  | 重新编译程序并执行，断点会保留。无法用于 attach 和 exec |

### 参数管理

对设置和查看变量、参数等

| 指令 | 缩写 | 作用 |
| --- | --- | --- |
| **print** | p | 查看变量或表达式的值 |
| whatis |  | 查看变量类型 |
| **args** |  | 查看函数的入参 |
| **locals** |  | 查看函数的局部变量 |
| **vars** |  | 查看全局变量 |
| set |  | 设置某个变量的值 |
| display |  | 将变量加入/移除监控列表、或查看监控列表 |

注意上述locals/vars/display都支持指定正则，具体用法可以使用`h locals`查看。

### 协程/线程切换

| 指令 | 缩写 | 作用 |
| --- | --- | --- |
| goroutine | gr | 查看或者切换到指定的goroutines.可以看到当前上下文 |
| goroutines | grs | 可以看到所有的goroutines的所处状态 |
| thread | tr |  |
| threads |  | 出线程所处状态 |

### 堆栈信息

| 指令 | 缩写 | 作用 |
| --- | --- | --- |
| stack | bt | 输出当前的堆栈 |

### 其他

| 指令 | 缩写 | 作用 |
| --- | --- | --- |
| exit | q | 退出调试会话 |
| disassemble | disass | 用于查看指定函数的汇编代码 |
| funcs |  | 查看函数，同样支持正则 |
| help | h | 查看使用手册 |
| list | ls/l | 查看源代码 |

## 附录

[https://github.com/go-delve/delve](https://github.com/go-delve/delve)