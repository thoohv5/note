---
title: 概述
date: 2026-04-15
---

## 概述

- **客户端真实 IP**（穿透代理 Nginx/CDN，最重要）
- **直接连接的代理 IP**（谁直接连了我的服务器）
- **服务器本机 IP**（我自己的 IP）

## Gin 框架（最常用）

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net"
)

func main() {
	r := gin.Default()

	r.GET("/ip", func(c *gin.Context) {
		// 1. 获取【客户端真实IP】（穿透代理：Nginx/CDN/SLB）
		clientRealIP := GetClientRealIP(c)

		// 2. 获取【直接连接服务器的代理IP/客户端IP】
		proxyIP := c.ClientIP() // Gin 自带，取直接连接方

		// 3. 获取【服务器本机IP】
		serverIP, _ := GetServerIP()

		// 返回结果
		c.JSON(200, gin.H{
			"client_real_ip": clientRealIP,
			"proxy_ip":       proxyIP,
			"server_ip":      serverIP,
		})
	})

	r.Run(":8080")
}

// GetClientRealIP 获取客户端真实IP（优先取代理头，兼容所有场景）
func GetClientRealIP(c *gin.Context) string {
	// 1. 先取 X-Real-IP（Nginx 最常用）
	ip := c.GetHeader("X-Real-IP")
	if ip != "" && net.ParseIP(ip) != nil {
		return ip
	}

	// 2. 再取 X-Forwarded-For（第一个就是真实IP）
	forwardIP := c.GetHeader("X-Forwarded-For")
	if forwardIP != "" {
		// 格式：真实IP, 代理1, 代理2...
		for i := 0; i < len(forwardIP); i++ {
			if forwardIP[i] == ',' {
				forwardIP = forwardIP[:i]
				break
			}
		}
		if net.ParseIP(forwardIP) != nil {
			return forwardIP
		}
	}

	// 3. 兜底：直接取连接IP
	return c.ClientIP()
}

// GetServerIP 获取服务器本机IP（排除回环 127.0.0.1）
func GetServerIP() (string, error) {
	addrs, err := net.InterfaceAddrs()
	if err != nil {
		return "", err
	}

	for _, addr := range addrs {
		ipNet, ok := addr.(*net.IPNet)
		if ok && !ipNet.IP.IsLoopback() && ipNet.IP.To4() != nil {
			return ipNet.IP.String(), nil
		}
	}
	return "127.0.0.1", nil
}
```

---

## Go 原生 net/http

```go
package main

import (
	"net"
	"net/http"
	"strings"
)

func main() {
	http.HandleFunc("/ip", func(w http.ResponseWriter, r *http.Request) {
		// 1. 客户端真实IP
		clientRealIP := getRealIP(r)

		// 2. 直接连接的代理IP
		proxyIP := getRemoteIP(r)

		// 3. 服务器IP
		serverIP, _ := GetServerIP()

		w.Write([]byte("client_real_ip: " + clientRealIP + "\n"))
		w.Write([]byte("proxy_ip: " + proxyIP + "\n"))
		w.Write([]byte("server_ip: " + serverIP + "\n"))
	})

	http.ListenAndServe(":8080", nil)
}

// getRealIP 原生获取客户端真实IP
func getRealIP(r *http.Request) string {
	ip := r.Header.Get("X-Real-IP")
	if ip != "" && net.ParseIP(ip) != nil {
		return ip
	}

	forward := r.Header.Get("X-Forwarded-For")
	if forward != "" {
		ip = strings.Split(forward, ",")[0]
		ip = strings.TrimSpace(ip)
		if net.ParseIP(ip) != nil {
			return ip
		}
	}

	ip, _, _ = net.SplitHostPort(r.RemoteAddr)
	return ip
}

// getRemoteIP 获取直接连接IP
func getRemoteIP(r *http.Request) string {
	ip, _, _ := net.SplitHostPort(r.RemoteAddr)
	return ip
}

// GetServerIP 服务器本机IP
func GetServerIP() (string, error) {
	addrs, err := net.InterfaceAddrs()
	if err != nil {
		return "", err
	}
	for _, addr := range addrs {
		if ipNet, ok := addr.(*net.IPNet); ok && !ipNet.IP.IsLoopback() && ipNet.IP.To4() != nil {
			return ipNet.IP.String(), nil
		}
	}
	return "127.0.0.1", nil
}
```

---

## 最重要的说明

### Nginx/CDN 配置

```c
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```

### K8s 配置

#### Ingress 配置（Nginx Ingress 最常用）

```yaml
annotations: nginx.ingress.kubernetes.io/use-forwarded-headers: "true" nginx.ingress.kubernetes.io/forwarded-for-header: "X-Forwarded-For"
```

#### Service 配置

```yaml
apiVersion: v1 
kind: Service 
metadata: 
	name: app-service 
spec: 
	type: ClusterIP # 若是 LoadBalancer 也一样生效
	externalTrafficPolicy: Local # ✅ 必须加 
	selector: 
		app: demo 
	ports: 
		- port: 80 
		  targetPort: 8080
```

#### 如果是 NodePort / LoadBalancer 服务（直接暴露，无 Ingress）

```yaml
spec: 
	type: LoadBalancer 
	externalTrafficPolicy: Local # ✅ 关键
```

### 区别总结

|字段|含义|来源|
|---|---|---|
|**客户端真实 IP**|用户本机 IP|X-Real-IP / X-Forwarded-For|
|**代理 IP**|谁直接连了我的服务器|RemoteAddr|
|**服务器 IP**|我的服务器 IP|本机网卡|
