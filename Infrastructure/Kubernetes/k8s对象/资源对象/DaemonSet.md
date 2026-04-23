---
title: DaemonSet
date: 2026-04-07
  - 基础设施
  - K8s
type: guide
status: complete
---

## DaemonSet

## 概述

`DaemonSet` 是 Kubernetes 中的一种特殊控制器，用于在 **每个节点（或指定节点）上运行一个 Pod 副本**。它非常适合需要在所有节点上运行的系统服务，比如：

- 日志收集器（如 Fluentd、Logstash）
- 监控代理（如 Prometheus Node Exporter）
- 网络插件（如 Calico, Cilium）
- 安全 agent（如 Falco, Trivy）工作原理

---

> **当你创建一个 DaemonSet，Kubernetes 会在每个符合条件的 Node 上自动调度并运行一个 Pod。新增节点时，DaemonSet 也会自动在新节点上拉起 Pod。**
> 

---

### 示例 YAML

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-logger
  namespace: kube-system
spec:
  selector:
    matchLabels:
      name: node-logger
  template:
    metadata:
      labels:
        name: node-logger
    spec:
      containers:
        - name: logger
          image: busybox
          command: ["sh", "-c", "while true; do echo Hello from $(hostname); sleep 10; done"]
```

---

### 📌 关键点说明

1. 每个 Node，会自动调度一个 Pod 副本
2. 新节点加入，自动部署对应 Pod
3. 删除 DaemonSet，所有对应的 Pod 会被清除

| 字段 | 说明 |
| --- | --- |
| `spec.selector` | 必须和 Pod 的 label 匹配 |
| `spec.template` | 定义 Pod 的模板 |

---

### 🔍 查看 DaemonSet 状态

```bash
kubectl get daemonset -n kube-system
kubectl describe daemonset <name> -n <namespace>
```

---

### 🎯 限定某些节点运行（通过 NodeSelector 或 Tolerations）

如果只想在特定节点上运行，比如标签是 `node-role.kubernetes.io/worker=true`：

```yaml
spec:
  template:
    spec:
      nodeSelector:
        node-role.kubernetes.io/worker: "true"

```

或者使用 `tolerations` 来容忍污点的节点（比如 master 节点）：

```yaml
tolerations:
  - key: "node-role.kubernetes.io/master"
    operator: "Exists"
    effect: "NoSchedule"
```

---

### 🔁 DaemonSet 与 Deployment 对比

| 特性 | DaemonSet | Deployment |
| --- | --- | --- |
| 控制 Pod 数量 | 每节点一个 | 自定义副本数 |
| 新增节点 | 自动拉起 | 不会自动部署新副本 |
| 适用场景 | 系统级 Agent | 应用服务 |
| 滚动升级 | 支持（从 K8s 1.6+） | 支持 |

---

### ⚙️ 滚动升级 DaemonSet（从 Kubernetes 1.6+ 开始支持）

```bash
kubectl rollout status daemonset <name> -n <namespace>
```

也支持 `kubectl set image daemonset/...` 方式更新镜像。