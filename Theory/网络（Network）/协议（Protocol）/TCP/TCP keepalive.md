# TCP keepalive

# 概述

### **keepalive机制**

TCP保活机制，就是为了保证连接的有效性，探测连接的对端是否存活的作用，在间隔一定的时间发探测包，根据回复来确认该连接是否有效。通常上层应用会自己提供心跳检测机制，而Linux内核本身也提供了从内核层面的确保连接有效性的方式。

**在双方交互过程中，可能存在以下的几种情况：**

- 客户端或者服务端意外断电、死机、进程挂掉重启等；
- 中间网络出现问题，连接双方无法知道一直等待；
- 程序问题导致的长时间CLOSE_WAIT问题；

此时，tcp keep-alive机制就可以解决大量无用连接无法回收、占用资源的问题了. KeepAlive并不是TCP协议规范的一部分，但在几乎所有的TCP/IP协议栈（不管是Linux还是Windows）中，都实现了KeepAlive功能，本片文章主要是基于linux操作系统上来进行说明。

## 术语

### 什么是TCP 保活

TCP keepalive 是一种允许 TCP 连接即使长时间没有数据交换也保持活动状态的机制。它的工作原理是从一个端点向另一端点发送定期探测或消息，并期望得到确认返回。如果在指定时间内未收到确认，则认为连接已失效并终止。 TCP keepalive 可以帮助检测和处理网络故障，例如电缆、路由器、防火墙或主机损坏。

### 为什么要使用TCP保活

TCP keepalive 可以为您的网络应用程序提供多种优势，例如避免不必要的重传和超时，从而降低性能和吞吐量。此外，它还可以通过关闭空闲或断开的连接来减少系统资源（如内存、套接字和端口）的消耗。此外，它可以通过检测网络问题并切换到备用路由或服务器来实现更快的恢复和故障转移。最后，它可以通过防止恶意或意外干扰 TCP 连接来提高数据的安全性和完整性。

### 如何启动TCP keepalive

TCP keepalive 默认情况下未启用，因为它可能并不适合所有场景和环境。要启用它，您需要在操作系统或应用程序级别修改一些参数，例如保活时间、间隔和计数。可以使用不同的工具和命令来更改这些参数，例如sysctl、ip、netsh 或setsockopt。此外，您应该查阅操作系统和应用程序文档以获取具体说明和建议。

### 如何监控TCP keepalive

TCP keepalive 是诊断和排除网络问题的有用工具，可提供有关 TCP 连接状态和运行状况的反馈。要监视 TCP keepalive，您可以使用 netstat 命令行实用程序来显示活动连接及其状态，使用 tcpdump 来捕获和显示网络接口上的流量，并使用 ss 命令行实用程序来显示有关 TCP 套接字的详细信息。此外，日志记录和警报机制可以记录并通知与 TCP keepalive 相关的任何异常或错误，例如连接超时或重置。

# 命令

```bash
# 查看
sysctl -a | grep keepalive

---
net.ipv4.tcp_keepalive_intvl = 75 
net.ipv4.tcp_keepalive_probes = 9
net.ipv4.tcp_keepalive_time = 7200
```

参数解释：

- `tcp_keepalive_time`，在TCP保活打开的情况下，最后一次数据交换到TCP发送第一个保活探测包的间隔，即允许的持续空闲时长，或者说每次正常发送心跳的周期，**默认值为7200s（2h）**（2h）。
- `tcp_keepalive_probes` 在tcp_keepalive_time之后，没有接收到对方确认，继续发送保活探测包次数，**默认值为9（次）**
- `tcp_keepalive_intvl`，在tcp_keepalive_time之后，没有接收到对方确认，继续发送保活探测包的发送频率，**默认值为75s。**

# 语言

### Golang

```go

package main

import (
	"fmt"
	"net"
	"os"
	"syscall"
	"time"
)

func main() {
	// 创建 TCP 地址
	addr, err := net.ResolveTCPAddr("tcp", "example.com:80")
	if err != nil {
		fmt.Println("Error resolving address:", err)
		os.Exit(1)
	}

	// 创建 TCP 连接
	conn, err := net.DialTCP("tcp", nil, addr)
	if err != nil {
		fmt.Println("Error connecting:", err)
		os.Exit(1)
	}
	defer conn.Close()

	// 获取底层文件描述符
	rawConn, err := conn.SyscallConn()
	if err != nil {
		fmt.Println("Error getting raw connection:", err)
		os.Exit(1)
	}

	// 设置 Keepalive 参数
	err = rawConn.Control(func(fd uintptr) {
		// 设置 Keepalive
		err := syscall.SetsockoptInt(int(fd), syscall.SOL_SOCKET, syscall.SO_KEEPALIVE, 1)
		if err != nil {
			fmt.Println("Error setting SO_KEEPALIVE:", err)
			return
		}

		// 设置 Keepalive 时间（秒）
		err = syscall.SetsockoptInt(int(fd), syscall.IPPROTO_TCP, syscall.TCP_KEEPIDLE, 7200)
		if err != nil {
			fmt.Println("Error setting TCP_KEEPIDLE:", err)
			return
		}

		// 设置 Keepalive 探测间隔时间（秒）
		err = syscall.SetsockoptInt(int(fd), syscall.IPPROTO_TCP, syscall.TCP_KEEPINTVL, 75)
		if err != nil {
			fmt.Println("Error setting TCP_KEEPINTVL:", err)
			return
		}

		// 设置 Keepalive 探测次数
		err = syscall.SetsockoptInt(int(fd), syscall.IPPROTO_TCP, syscall.TCP_KEEPCNT, 9)
		if err != nil {
			fmt.Println("Error setting TCP_KEEPCNT:", err)
			return
		}
	})

	if err != nil {
		fmt.Println("Error setting TCP Keepalive:", err)
		os.Exit(1)
	}

	// 示例：保持连接活动
	for {
		time.Sleep(10 * time.Second)
		_, err := conn.Write([]byte("Keepalive message"))
		if err != nil {
			fmt.Println("Error writing data:", err)
			os.Exit(1)
		}
	}
}

```

# 注意

**`http 1.0`中默认是关闭的**，需要在http头加入"Connection: Keep-Alive"，才能启用Keep-Alive；**`http 1.1`中默认启用Keep-Alive**，如果加入"Connection: close "，才关闭。目前大部分浏览器都是用http1.1协议，也就是说默认都会发起Keep-Alive的连接请求了，所以是否能完成一个完整的Keep-Alive连接就看服务器设置情况。

# 附录

[阿里-马云的学习笔记](https://www.cnblogs.com/alimayun/p/12667204.html)

[浅谈TCP的keep-alive机制_风筝Lee的博客-CSDN博客_tcp-keepalive](https://blog.csdn.net/breakout_alex/article/details/109001575)