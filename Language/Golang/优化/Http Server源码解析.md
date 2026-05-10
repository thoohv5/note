---
title: golang Http server包分析 二 源码解析
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
---

## 源码解析

Go `net/http` 包的核心流程。

### ListenAndServe

```go
func ListenAndServe(addr string, handler Handler) error {
    server := &Server{Addr: addr, Handler: handler}
    return server.ListenAndServe()
}
```

创建 Server 实例并启动。`ListenAndServe` 内部调用 `net.Listen("tcp", addr)` 打开 TCP 监听，然后 `srv.Serve(tcpKeepAliveListener{ln})`。

### Serve 主循环

```go
func (srv *Server) Serve(l net.Listener) error {
    for {
        rw, err := l.Accept()
        c := srv.newConn(rw)
        go c.serve(context)  // 每个连接一个 goroutine
    }
}
```

- `Accept()` 阻塞等待 TCP 连接
- 每个连接创建 `conn` 对象，在独立 goroutine 中执行 `serve()`
- 网络异常重试：tempDelay 指数退避（5ms→1s max）

### conn.serve

- 设置读写超时（ReadTimeout/WriteTimeout/TLS 处理）
- 循环读取请求：`c.readRequest(ctx)` 解析 HTTP 请求
- `serverHandler{c.server}.ServeHTTP(w, w.req)` 调用用户注册的 Handler
- 复用连接：HTTP/1.1 默认 keep-alive，通过 `closeWriter` 控制

### Handler 接口

```go
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```

`http.HandlerFunc` 将普通函数适配为 Handler。`ServeMux` 实现路径匹配和路由分发。

## 关键点

- HTTP/1.1 管道化：一个连接可排队多个请求
- `ResponseWriter` 在首次 Write 时自动发送 Header
- `Request.Body` 需调用方关闭

## 参考

- [golang Http server 包分析 - 源码解析](https://liangtian.me/post/golang-http-2/)
- [[Language/Golang/优化/Http Server源码解析.md]]
- [[net/http]]