---
title: Service
date: 2026-04-07
tags: [基础设施, K8s, 对象]
type: reference
status: complete
---

## Service

## 概述

Pod 是动态创建、销毁的，它们的 IP 是不固定的。**Service 解决了 Pod 网络不稳定的问题**，提供了一个**长期不变的访问入口**，并能根据标签自动找到后端的 Pod。

## **概念**

`Service` 是 `Kubernetes` 中用于定义一组 `Pod` 访问策略的抽象层，主要功能：

- **服务发现**：为动态变化的 `Pod` 提供稳定的访问端点
- **负载均衡**：在多个 `Pod` 实例间分配流量
- **抽象后端**：解耦前端应用与后端 `Pod` 的直接依赖

### **类型**

### `ClusterIP` 【**默认类型**】

- **作用**：仅集群内部访问，最常用于服务间通信。
- **特点**：集群外不可访问。
- **访问方式**：`http://<service-name>.<namespace>.svc.cluster.local`
- 示例YAML
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: my-service
      namespace: default
    spec:
      type: NodePort
      selector:
        app: myapp
      ports:
        - protocol: TCP     # 协议，默认TCP
    	    port: 80          # Service的的端口，对外暴露（集群内）
          targetPort: 8080  # Pod的端口，**默认与 port 相同**
    ```
    

验证方式

```yaml
kubectl exec -it <pod_name> -- nslookup demo.default.svc.cluster.local
```

- **Headless Service**
    
    可以看到，所谓的 Headless Service，其实仍是一个标准 Service 的 YAML 文件。只不过，它的 clusterIP 字段的值是：None，即：这个 Service，没有一个 VIP 作为“头”。这也就是 Headless 的含义。所以，这个 Service 被创建后并不会被分配一个 VIP，而是会以 DNS 记录的方式暴露出它所代理的 Pod。
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      ports:
      - port: 80
        name: web
      clusterIP: None
      selector:
        app: nginx
    ```
    
    serviceName=nginx 就是告诉 StatefulSet 控制器，在执行控制循环（Control Loop）的时候，请使用 nginx 这个 Headless Service 来保证 Pod 的“可解析身份”。
    
    ```yaml
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web
    spec:
      serviceName: "nginx"
      replicas: 2
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.9.1
            ports:
            - containerPort: 80
              name: web
    ```
    

### `NodePort`

- **作用**：集群外可访问，通过集群任意 Node 的 IP + 固定端口访问。端口范围默认：30000-32767。
- **特点**：适合测试、开发环境或局域网内访问。
- **访问方式**：`http://<NodeIP>:<nodePort>`
- 示例YAML
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: my-service
      namespace: default
    spec:
      type: NodePort
      selector:
        app: myapp
      ports:
        - protocol: TCP     # 协议，默认TCP
    	    port: 80          # Service的的端口，对外暴露（集群内）
          targetPort: 8080  # Pod的端口，**默认与 port 相同**
          nodePort: 30080   # Node的端口，对外暴露（集群外）
    ```
    

### `LoadBalancer`

- **作用**：将服务暴露为云服务商提供的公网负载均衡地址。
- **特点**：仅在公有云环境下生效（如 AWS、GCP、阿里云）。
- **访问方式**：服务暴露后会获取一个公网 IP。
- 示例 YAML
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: my-service
      namespace: default
    spec:
      type: LoadBalancer
      selector:
        app: myapp
      ports:
        - port: 80
          targetPort: 8080
          protocol: TCP
    ```
    

### `ExternalName`

- **作用**：将服务名解析为外部 DNS 地址。
- **特点**：不会创建真正的代理，仅 DNS 解析。
- **访问方式**：访问 Service 名时自动跳转到外部域名。
- 示例 YAML
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: my-service
      namespace: default
    spec:
      type: ExternalName
      externalName: api.example.com
    
    ```
    

### 对比

| 类型 | 是否可集群外访问 | 是否需要云服务支持 | 用途 |
| --- | --- | --- | --- |
| ClusterIP | ❌ | ❌ | 内部服务通信 |
| NodePort | ✅（NodeIP:端口） | ❌ | 简单外部访问 |
| LoadBalancer | ✅（公网 IP） | ✅ | 云上生产访问 |
| ExternalName | ✅（DNS 解析） | ❌ | 外部服务代理 |