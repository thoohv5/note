---
title: Ingres
date: 2026-04-07
tags: [基础设施, K8s, 对象]
type: reference
status: complete
---

## Ingres

**`Ingress`** 是 `Kubernetes` 中用于 **管理外部访问集群服务** 的资源对象，通常用于 `HTTP` 和 `HTTPS` 请求的路由控制。通过 `Ingress`，能够将外部请求引导到不同的服务，并且支持负载均衡、SSL 终止、基于域名或路径的路由等功能。

---

## 概述

**目的**：让外部流量可以访问 `Kubernetes` 集群内部的服务，通常用于 Web 应用的外部访问。

**支持功能**：

- **基于 Host 和 Path 路由**：将不同的路径或域名请求转发到不同的后端服务。
- **负载均衡**：自动平衡请求流量到多个 Pod。
- **SSL/TLS 终止**：支持 HTTPS 请求，解密流量并将其传递到后端服务。
- **基于规则的流量控制**：可以设置复杂的流量转发规则（例如路径重写、跳转、镜像等）。

---

### 基本结构

- 示例
    
    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: demo-ingress
      annotations:
        nginx.ingress.kubernetes.io/rewrite-target: /
    spec:
      rules:
        - host: demo-interface.local
          http:
            paths:
              - path: /
                pathType: Prefix
                backend:
                  service:
                    name: dmo-interface-svc
                    port:
                      number: 8080
    
    ```
    
    ### 关键字段说明：
    
    - `apiVersion`：Ingress API 版本，通常是 `networking.k8s.io/v1`。
    - `kind`：资源类型，固定为 `Ingress`。
    - `metadata`：Ingress 的元数据，例如名称、标签等。
    - `spec.rules`：定义流量路由规则。
        - `host`：指定的主机名（域名）。
        - `http.paths`：匹配路径的规则，可以有多个路径，每个路径可以指向不同的服务。
    - `backend`：后端服务和端口。

---

### 功能与用法

### 1. **HTTP**

```yaml
spec:
  rules:
    - host: myapp.example.com
      http:
        paths:
          - path: /api
            backend:
              service:
                name: api-service
                port:
                  number: 80
          - path: /web
            backend:
              service:
                name: web-service
                port:
                  number: 80

```

### 2. **SSL/TLS 支持（HTTPS）**

Ingress 还支持 **SSL/TLS 终止**，可以通过指定证书的 Secret 来启用 HTTPS。

```yaml
spec:
  tls:
    - hosts:
        - myapp.example.com
      secretName: my-tls-secret
  rules:
    - host: myapp.example.com
      http:
        paths:
          - path: /
            backend:
              service:
                name: myapp-service
                port:
                  number: 80

```

- `tls`：指定使用的证书 Secret（例如 `myapp-tls-secret`）。
- `secretName`：TLS 证书所在的 Secret 名称。

---

### TLS Secret

```bash
kubectl create secret tls my-tls-secret --cert=/path/to/tls.crt --key=/path/to/tls.key
```

- `tls.crt`：是证书文件。
- `tls.key`：是证书的私钥文件。

### **Ingress 与 Gateway 的比较**

| 特性 | **Ingress** (Kubernetes 原生) | **Gateway** (Istio) |
| --- | --- | --- |
| **流量类型** | 主要是 HTTP 和 HTTPS | 支持 HTTP、HTTPS、gRPC、TCP、WebSocket 等 |
| **功能** | 简单的流量路由、负载均衡、TLS 终止 | 高级流量管理、流量分配、故障注入、安全性等 |
| **配置复杂度** | 配置简单，适合基础场景 | 配置较复杂，适用于复杂的流量控制需求 |
| **协议支持** | 仅支持 HTTP 和 HTTPS | 支持多种协议 |
| **安全性** | 基本的 TLS 终止 | 支持 mTLS、认证、授权等高级安全策略 |
| **服务网格集成** | 与服务网格无关 | 是 Istio 服务网格的一部分，与 Istio 集成 |
| **流量管理能力** | 基于路径的简单路由 | 更强大的流量控制，支持流量分割、A/B 测试、蓝绿部署等 |