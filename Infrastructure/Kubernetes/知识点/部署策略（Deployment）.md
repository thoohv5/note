---
title: 部署策略（Deployment）
date: 2026-04-07
tags: [基础设施, K8s]
type: note
status: complete
---

# 部署策略（Deployment）

### 蓝绿部署（Blue-Green Deployment）

运行两个环境（蓝色和绿色），一个在线提供服务，另一个作为新版本的预发布环境，验证无误后快速切换流量。

- 1. 创建两个 Deployment
    
    ### ✅ Blue（生产中的旧版本）：
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: my-app-blue
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: my-app
          version: blue
      template:
        metadata:
          labels:
            app: my-app
            version: blue
        spec:
          containers:
            - name: app
              image: my-app:v1
    
    ```
    
    ### 🆕 Green（新版本）：
    
    ```yaml
    yaml
    复制编辑
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: my-app-green
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: my-app
          version: green
      template:
        metadata:
          labels:
            app: my-app
            version: green
        spec:
          containers:
            - name: app
              image: my-app:v2
    
    ```
    
- 2. 创建一个 Service（流量切换关键）
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: my-app-service
    spec:
      selector:
        app: my-app
        version: blue  # 初始指向 blue
      ports:
        - port: 80
          targetPort: 80
    ```
    
- 3. 蓝绿切换操作
    
    ```yaml
    kubectl patch service my-app-service -p '{"spec":{"selector":{"app":"my-app","version":"green"}}}'
    ```
    

### 金丝雀发布（Canary Deployment）

**金丝雀发布（Canary Deployment）** 是一种 **渐进式升级策略**，它允许你将新版本的应用部署到一小部分用户中，观察一段时间，如果没有问题，再逐步放大流量，最终全量发布。

这个策略名字来源于“煤矿中的金丝雀”，早期矿工会带着金丝雀下矿井，用它是否异常来判断空气是否有毒。K8s 中的“金丝雀发布”有类似的含义：**先让少部分流量试试水**。

- 使用 **Istio + VirtualService + DestinationRule**（推荐）
    
    ```yaml
    ---
    # 命名空间（如已存在可跳过）
    apiVersion: v1
    kind: Namespace
    metadata:
      name: pro
    ---
    # v1 Deployment
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: demo-interface-deploy-v1
      namespace: pro
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: demo-interface
          version: v1
      template:
        metadata:
          labels:
            app: demo-interface
            version: v1
        spec:
          containers:
            - name: demo
              image: <your-image>:v1
              ports:
                - containerPort: 8080
    ---
    # v2 Deployment
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: demo-interface-deploy-v2
      namespace: pro
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: demo-interface
          version: v2
      template:
        metadata:
          labels:
            app: demo-interface
            version: v2
        spec:
          containers:
            - name: demo
              image: <your-image>:v2
              ports:
                - containerPort: 8080
    ---
    # Service（统一访问入口）
    apiVersion: v1
    kind: Service
    metadata:
      name: demo-interface-svc
      namespace: pro
    spec:
      selector:
        app: demo-interface
      ports:
        - name: http
          port: 18080
          targetPort: 8080
    ---
    # Gateway（外部访问入口）
    apiVersion: networking.istio.io/v1beta1
    kind: Gateway
    metadata:
      name: demo-interface-gateway
      namespace: pro
    spec:
      selector:
        istio: ingressgateway
      servers:
        - port:
            number: 80
            name: http
            protocol: HTTP
          hosts:
            - demo-interface.local
    ---
    # DestinationRule（定义版本子集）
    apiVersion: networking.istio.io/v1beta1
    kind: DestinationRule
    metadata:
      name: demo-interface-dr
      namespace: pro
    spec:
      host: demo-interface-svc
      subsets:
        - name: v1
          labels:
            version: v1
        - name: v2
          labels:
            version: v2
    ---
    # VirtualService（金丝雀流量控制）
    apiVersion: networking.istio.io/v1beta1
    kind: VirtualService
    metadata:
      name: demo-interface-vs
      namespace: pro
    spec:
      hosts:
        - demo-interface.local
      gateways:
        - demo-interface-gateway
      http:
        - route:
            - destination:
                host: demo-interface-svc
                subset: v1
              weight: 80
            - destination:
                host: demo-interface-svc
                subset: v2
              weight: 20
    
    ```