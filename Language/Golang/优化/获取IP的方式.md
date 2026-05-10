---
title: 获取IP的方式
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: guide
status: complete
---

## 获取IP的方式

---

### 逻辑链路全景图

当一个请求从用户端发出，经过代理（如 Nginx），到达你的 Go 服务时，IP 的变化如下：

- **用户的真实 IP**：最初发起请求的地址。
- **X-Forwarded-For (XFF)**：代理服务器在转发时，把用户的 IP 塞进这个 Header。
- **RemoteAddr (原生)**：对你的 Go 程序来说，直接跟它握手的是 Nginx，所以这里拿到的是 **Nginx 的 IP**。

---

### Gin 的双层获取机制

### 第一层：`c.RemoteIP()` —— “谁在敲我的门？”

这是 Gin 对原生 `r.RemoteAddr` 的简单封装。它做的唯一一件事就是**剔除端口号**。

- **用途**：如果你想知道请求是不是从自家的 Nginx 转发过来的，看这个值。
- **代码行为**：直接取底层连接信息，不看任何 HTTP Header。

### 第二层：`c.ClientIP()` —— “谁才是真正的发起者？”

这是 Gin 最常用的方法，它的逻辑比较“聪明”：

1. **先看 `RemoteIP()`**：如果它是你信任的代理（在 `SetTrustedProxies` 列表中）。
2. **再看 Header**：它会去读 `X-Forwarded-For` 或 `X-Real-IP`。
3. **防伪造策略**：如果 `RemoteIP()` **不是**信任的代理，Gin 会直接返回 `RemoteIP()`。
    - *原因*：如果随便一个陌生 IP 发来一个带 `X-Forwarded-For: 8.8.8.8` 的请求，而你直接信了，那么用户就能通过伪造 Header 绕过你的 IP 黑名单。

---

### 最佳实践建议

### 场景 A：直接暴露在公网（无代理）

这种情况下，`RemoteIP()` 和 `ClientIP()` 通常是一样的。

- **建议**：直接用 `c.ClientIP()`。

### 场景 B：在 Nginx/CDN 后端（有代理）

这是最常见的生产环境。

- **配置**：必须在 Gin 初始化时设置 `router.SetTrustedProxies([]string{"Nginx的内网IP"})`。
- **使用**：调用 `c.ClientIP()` 获取用户 IP。
- **避坑**：如果发现获取到的是内网 IP，说明 Nginx 没传 `X-Forwarded-For` 或者你没把 Nginx 的 IP 加到信任列表。