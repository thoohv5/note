---
title: CA证书代码
date: 2026-04-07
tags: [理论, 安全]
type: reference
status: complete
---

# CA证书代码

# 概述

HTTPS利用的是对称与非对称加密算法结合的方式。

HTTPS服务端在连接建立过程（ssl shaking握手协议）中，会将自身的公钥发送给客户端。客户端拿到公钥后，与服务端协商数据传输通道的对称加密密钥-对话密钥，随后的这个协商过程则 是基于非对称加密的（因为这时客户端已经拿到了公钥，而服务端有私钥）。一旦双方协商出对话密钥，则后续的数据通讯就会一直使用基于该对话密钥的对称加密算法了。

## **HTTPS握手流程**：

- **步骤1**：服务端发送SSL证书（含公钥）给客户端。
    - *此时客户端已持有服务端的公钥，但私钥始终只在服务端*。
- **步骤2**：客户端生成一个**随机的对称密钥**（即"对话密钥"）。
    - 用服务端的公钥加密这个对称密钥，发送给服务端。
    - *这一步是关键：用非对称加密保护对称密钥的传输*。
- **步骤3**：服务端用私钥解密获取对称密钥。
    - *此后双方都安全地拥有了相同的对称密钥*。
- **步骤4**：后续通信全部使用这个对称密钥加密数据。

# 概念

## **标准 HTTPS（单向 TLS）**

- **只有服务器验证**：
    - 客户端验证服务器的身份（通过服务器的 SSL 证书）。
    - 服务器 **不验证客户端**，任何知道服务器地址的客户端都可以连接（比如普通网页访问）。
- **加密过程**（如你之前描述的）：
    1. 服务器发送自己的公钥（包含在证书中）给客户端。
    2. 客户端生成 **对称密钥**，用服务器的公钥加密后发送给服务器。
    3. 服务器用私钥解密，获得对称密钥，后续通信使用对称加密。

---

## **双向TLS（Mutual TLS，mTLS）**

- **客户端和服务器互相验证**：
    - 服务器需要验证客户端的身份（客户端也必须提供证书）。
    - 适用于高安全场景，如银行系统、微服务间通信、API 认证等。
- **加密过程**（比单向 TLS 多一步客户端认证）：
    1. **服务器发送证书**（含公钥）给客户端，客户端验证服务器身份。
    2. **客户端发送自己的证书**（含公钥）给服务器，服务器验证客户端身份。
    3. **密钥协商**：
        - 客户端生成 **对称密钥**，用服务器的公钥加密后发送。
        - （可选）服务器也可能生成密钥材料，用客户端的公钥加密发送。
    4. 双方计算最终的 **会话密钥**，后续通信使用对称加密。

# 代码

## 服务端

### **标准 HTTPS（单向 TLS）**

```go
package main

import (
    "fmt"
    "net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hi, This is an example of https service in golang!")
}

func main() {
    http.HandleFunc("/", handler)
    http.ListenAndServeTLS(":8081", "server.crt", "server.key", nil)
}

```

### **双向TLS（mTLS）**

```go
package main

import (
    "crypto/tls"
    "crypto/x509"
    "fmt"
    "io/ioutil"
    "net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Mutual TLS (Client and Server Authentication)")
}

func main() {
    // 1. 加载 CA 证书（用于验证客户端证书）
    caCert, _ := ioutil.ReadFile("ca.crt")
    caCertPool := x509.NewCertPool()
    caCertPool.AppendCertsFromPEM(caCert)

    // 2. 配置 TLS，要求客户端提供有效证书
    tlsConfig := &tls.Config{
        ClientCAs:  caCertPool,
        ClientAuth: tls.RequireAndVerifyClientCert, // 强制验证客户端
    }

    server := &http.Server{
        Addr:      ":443",
        Handler:   http.HandlerFunc(handler),
        TLSConfig: tlsConfig,
    }

    // 3. 启动服务（需服务器证书）
    server.ListenAndServeTLS("server.crt", "server.key")
}
```

## 客户端

### 跳过验证

```go
//gohttps/4-https/client2.go
package main

import (
    "crypto/tls"
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    tr := &http.Transport{
        TLSClientConfig:    &tls.Config{InsecureSkipVerify: true},
    }
    client := &http.Client{Transport: tr}
    resp, err := client.Get("<https://localhost:8081>")

    if err != nil {
        fmt.Println("error:", err)
        return
    }
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    fmt.Println(string(body))
}

```

### 标准HTTPS

```bash
package main

import (
    "crypto/tls"
    "crypto/x509"
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    // 1. 加载 CA 证书（用于验证服务器证书）
    caCert, err := ioutil.ReadFile("ca.crt") // 可以是系统 CA 或自定义 CA
    if err != nil {
        fmt.Println("Failed to load CA cert:", err)
        return
    }

    caCertPool := x509.NewCertPool()
    caCertPool.AppendCertsFromPEM(caCert)

    // 2. 配置 TLS（验证服务器证书）
    tlsConfig := &tls.Config{
        RootCAs: caCertPool, // 设置受信任的 CA
    }

    client := &http.Client{
        Transport: &http.Transport{TLSClientConfig: tlsConfig},
    }

    // 3. 发送 HTTPS 请求
    resp, err := client.Get("https://example.com")
    if err != nil {
        fmt.Println("Request failed:", err)
        return
    }
    defer resp.Body.Close()

    body, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(body))
}
```

### 双向TLS

```go
package main

import (
    "crypto/tls"
    "crypto/x509"
    "io/ioutil"
    "net/http"
    "fmt"
)

func main() {
    // 1. 加载客户端证书和私钥
    cert, _ := tls.LoadX509KeyPair("client.crt", "client.key")

    // 2. 加载 CA 证书（用于验证服务器证书）
    caCert, _ := ioutil.ReadFile("ca.crt")
    caCertPool := x509.NewCertPool()
    caCertPool.AppendCertsFromPEM(caCert)

    // 3. 配置 TLS
    tlsConfig := &tls.Config{
        Certificates: []tls.Certificate{cert}, // 客户端证书
        RootCAs:      caCertPool,              // 验证服务器的 CA
    }

    client := &http.Client{
        Transport: &http.Transport{TLSClientConfig: tlsConfig},
    }

    // 4. 发送请求
    resp, _ := client.Get("https://server.example.com")
    defer resp.Body.Close()
    body, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(body))
}

```

# 附录

[﹎斑斓的梦ぷ](https://www.cnblogs.com/pzblog/p/9088286.html)