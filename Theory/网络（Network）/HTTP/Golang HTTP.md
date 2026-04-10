---
title: Golang HTTP
date: 2026-04-07
tags: [理论, 网络]
type: note
status: complete
---

# Golang HTTP

```erlang
var DefaultTransport RoundTripper = &Transport{
	Proxy: ProxyFromEnvironment,
	DialContext: defaultTransportDialContext(&net.Dialer{
		Timeout:   30 * time.Second,
		KeepAlive: 30 * time.Second,
	}),
	ForceAttemptHTTP2:     true,
	MaxIdleConns:          100,
	IdleConnTimeout:       90 * time.Second,
	TLSHandshakeTimeout:   10 * time.Second,
	ExpectContinueTimeout: 1 * time.Second,
}

// DefaultMaxIdleConnsPerHost is the default value of [Transport]'s
// MaxIdleConnsPerHost.
const DefaultMaxIdleConnsPerHost = 2

// A Client is an HTTP client. Its zero value ([DefaultClient]) is a
// usable client that uses [DefaultTransport].
//
// The [Client.Transport] typically has internal state (cached TCP
// connections), so Clients should be reused instead of created as
// needed. Clients are safe for concurrent use by multiple goroutines.
//
// A Client is higher-level than a [RoundTripper] (such as [Transport])
// and additionally handles HTTP details such as cookies and
// redirects.
//
// When following redirects, the Client will forward all headers set on the
// initial [Request] except:
//
//   - when forwarding sensitive headers like "Authorization",
//     "WWW-Authenticate", and "Cookie" to untrusted targets.
//     These headers will be ignored when following a redirect to a domain
//     that is not a subdomain match or exact match of the initial domain.
//     For example, a redirect from "foo.com" to either "foo.com" or "sub.foo.com"
//     will forward the sensitive headers, but a redirect to "bar.com" will not.
//   - when forwarding the "Cookie" header with a non-nil cookie Jar.
//     Since each redirect may mutate the state of the cookie jar,
//     a redirect may possibly alter a cookie set in the initial request.
//     When forwarding the "Cookie" header, any mutated cookies will be omitted,
//     with the expectation that the Jar will insert those mutated cookies
//     with the updated values (assuming the origin matches).
//     If Jar is nil, the initial cookies are forwarded without change.
type Client struct {
	// Transport specifies the mechanism by which individual
	// HTTP requests are made.
	// If nil, DefaultTransport is used.
	Transport RoundTripper

	// CheckRedirect specifies the policy for handling redirects.
	// If CheckRedirect is not nil, the client calls it before
	// following an HTTP redirect. The arguments req and via are
	// the upcoming request and the requests made already, oldest
	// first. If CheckRedirect returns an error, the Client's Get
	// method returns both the previous Response (with its Body
	// closed) and CheckRedirect's error (wrapped in a url.Error)
	// instead of issuing the Request req.
	// As a special case, if CheckRedirect returns ErrUseLastResponse,
	// then the most recent response is returned with its body
	// unclosed, along with a nil error.
	//
	// If CheckRedirect is nil, the Client uses its default policy,
	// which is to stop after 10 consecutive requests.
	CheckRedirect func(req *Request, via []*Request) error

	// Jar specifies the cookie jar.
	//
	// The Jar is used to insert relevant cookies into every
	// outbound Request and is updated with the cookie values
	// of every inbound Response. The Jar is consulted for every
	// redirect that the Client follows.
	//
	// If Jar is nil, cookies are only sent if they are explicitly
	// set on the Request.
	Jar CookieJar

	// Timeout specifies a time limit for requests made by this
	// Client. The timeout includes connection time, any
	// redirects, and reading the response body. The timer remains
	// running after Get, Head, Post, or Do return and will
	// interrupt reading of the Response.Body.
	//
	// A Timeout of zero means no timeout.
	//
	// The Client cancels requests to the underlying Transport
	// as if the Request's Context ended.
	//
	// For compatibility, the Client will also use the deprecated
	// CancelRequest method on Transport if found. New
	// RoundTripper implementations should use the Request's Context
	// for cancellation instead of implementing CancelRequest.
	Timeout time.Duration
}
```

## Client参数

### `Transport` (底层连接池)

- **默认值**：如果为 `nil`，则使用 `http.DefaultTransport`。
- **核心逻辑**：它是 HTTP 请求的“发动机”，负责 TCP 建立、TLS 握手以及 **连接复用（Keep-Alive）**。
- **风险点**：`DefaultTransport` 的 `MaxIdleConnsPerHost` 默认为 **2**。
- **建议**：**生产环境务必自定义** `Transport`。

### `CheckRedirect` (重定向策略)

- **默认值**：如果为 `nil`，默认重定向 **10 次**后停止。
- **核心逻辑**：每当服务器返回 301/302 时，此函数决定是否继续。
- **风险点**：注意注释里提到的安全逻辑。如果你重定向到了一个**不同域名**的目标，`Authorization` 等敏感 Header 会被丢弃。如果你的请求依赖这些 Header，重定向后的请求会失败（虽然通常报 401，但有时也会导致连接异常断开）。

### `Jar` (Cookie 管理)

- **默认值**：`nil`。
- **核心逻辑**：如果为 `nil`，你必须在每个 `Request` 对象中手动设置 `Cookie` Header，且重定向时不会自动携带新产生的 Cookie。

### `Timeout` (全过程计时器)

- **默认值**：`0`（代表**永不超时**）。
- **覆盖范围**：包括连接时间、重定向时间、以及**读取 Response Body 的时间**。
- **相关参数**
    1. **总超时**`http.Client.Timeout`整个 HTTP 事务（从开始到 Body 读完）。
    2. **连接超时**`Transport.DialContext.Timeout`仅限 TCP 建立连接的那一刻。
    3. **握手超时**`Transport.TLSHandshakeTimeout`仅限 HTTPS 加密握手阶段。
    4. **空闲超时**`Transport.IdleConnTimeout`连接不用时，在池子里能活多久（与 EOF 强相关）。

## DefaultTransport参数

### **`Proxy: ProxyFromEnvironment`**

- **功能**：设置代理服务器。
- **介绍**：默认值 `ProxyFromEnvironment` 表示从系统的环境变量（如 `HTTP_PROXY`, `HTTPS_PROXY`, `NO_PROXY`）中读取代理配置。
- **影响**：如果你的服务器配置了错误的代理环境变量，请求会尝试走代理，失败后可能直接报连接错误。

### `DialContext` (含 `Timeout` 和 `KeepAlive`)

- **功能**：定义如何建立底层的 TCP 连接。
- **Timeout (30s)**：TCP 三次握手的最长等待时间。如果在 30 秒内连不上服务器（比如网络不通、防火墙拦截），直接报错。
- **KeepAlive (30s)**：**TCP 层的保活探测**。每隔 30 秒发送一个探测包，确保这条物理通道还没断。这能防止防火墙因为连接长时间无数据而悄悄将其剔除。

### `ForceAttemptHTTP2: true`

- **功能**：强制尝试升级到 HTTP/2 协议。
- **介绍**：如果服务器支持 HTTP/2，Go 会优先使用。HTTP/2 在一个 TCP 连接上并发处理多个请求（多路复用）。
- **影响**：这能提高效率，但如果底层连接断开，所有正在进行的并发请求会**集体报错 EOF**。

### `MaxIdleConns: 100`

- **功能**：**全局**连接池的最大空闲连接数。
- **介绍**：你的程序可能同时访问微信、支付宝、自己的后端。这个参数规定了在连接池里一共能攒多少个不用的“活”连接。

### `IdleConnTimeout: 90 * time.Second`

- **功能**：空闲连接的寿命。
- **介绍**：一个连接在完成请求后回到池子里，如果 90 秒内没人再用它，Go 会主动把它物理关闭。
- **原则：客户端的 `IdleConnTimeout` 必须明显短于服务端的超时时间。**
    1. **如果对方是 Nginx**（默认 65s）：建议设置为 **30s - 60s**。
    2. **如果对方是 AWS/云网关**（通常 60s）：建议设置为 **30s**。
    3. **如果你追求极致稳定**：甚至可以设置为 **15s**。

### `TLSHandshakeTimeout: 10 * time.Second`

- **功能**：TLS/SSL 握手的超时时间。
- **介绍**：在 TCP 连接建好后，进行加密证书交换的时间。
- **影响**：如果你的服务器在海外，或者微信服务器瞬时高负载，10 秒不够用就会报错。

### `ExpectContinueTimeout: 1 * time.Second`

- **功能**：控制 `Expect: 100-continue` 的等待时间。
- **介绍**：通常用于大数据上传，先问服务器“能不能发”，服务器 1 秒内没回，客户端就直接开传。

### `DefaultMaxIdleConnsPerHost = 2` (最坑参数)

- **功能**：**每个目标主机**（域名）保留的最大空闲连接数。
- **介绍**：虽然全局可以存 100 个连接，但默认对**同一个域名**只留 2 个。
- **典型后果**：
    1. 你并发请求微信 10 次。
    2. 请求全部结束后，Go 发现池子里只能存 2 个连接。
    3. 于是 Go **手动关闭了剩下的 8 个连接**。
    4. 这种频繁的“自杀式”关闭连接，极大地增加了网络抖动时出现 EOF 的概率。

### 默认配置

```erlang
// 建议的生产环境配置
var transport = &http.Transport{
    // 1. 解决高并发频繁关连问题
    // 只要全局没超，允许同一个 Host 保留更多长连接
    MaxIdleConnsPerHost: 100, 
    MaxIdleConns:        100,

    // 2. 解决复用死连接导致的 EOF
    // 缩短到 30s，确保在服务器（通常 60s）之前主动关闭旧连接
    IdleConnTimeout: 30 * time.Second,

    // 3. 保持拨号和 TLS 超时
    DialContext: (&net.Dialer{
        Timeout:   30 * time.Second,
        KeepAlive: 30 * time.Second,
    }).DialContext,
    TLSHandshakeTimeout: 10 * time.Second,
}

client.httpClient = &http.Client{
    Timeout: 10 * time.Second, 
    
    Transport: transport
}
```