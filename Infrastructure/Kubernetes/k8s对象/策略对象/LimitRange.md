---
title: LimitRange
date: 2026-04-07
  - 基础设施
  - K8s
type: guide
status: complete
---

## LimitRange

## 概述

`LimitRange` 是 Kubernetes 中的一种 **资源限制策略**，用于对 **Pod 或容器的资源请求与限制（CPU/内存）设定默认值和上限**，确保资源使用的规范性，特别适用于多租户环境或开发团队共享集群的场景。

---

## 作用

在命名空间内设定：

1. **默认值**（如果 Pod 没写，自动补上）：
    - `default`：未设置 `resources.limits` 时自动加上的默认值
    - `defaultRequest`：未设置 `resources.requests` 时自动加上的默认值
2. **最小值 / 最大值**：
    - `min`: 每个容器/Pod 的最小资源值，低于会被拒绝
    - `max`: 每个容器/Pod 的最大资源值，超过会被拒绝
3. **比率限制**：
    - 限制 `request <= limit`

---

### 示例 YAML

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: limit-range-example
  namespace: dev
spec:
  limits:
    - type: Container
      max:
        cpu: "2"
        memory: "1Gi"
      min:
        cpu: "100m"
        memory: "128Mi"
      default:
        cpu: "500m"
        memory: "512Mi"
      defaultRequest:
        cpu: "200m"
        memory: "256Mi"
      maxLimitRequestRatio:
        cpu: "10"
```

---

### 含义解读：

| 字段 | 含义 |
| --- | --- |
| `max` | 每个容器最多能申请的资源，超出不允许创建 |
| `min` | 每个容器最少要申请的资源，低于不允许创建 |
| `default` | 如果容器没写 `resources.limits`，自动补上这个值 |
| `defaultRequest` | 如果容器没写 `resources.requests`，自动补上这个值 |
| `maxLimitRequestRatio` | 限制 `limit/request` 比例，比如 CPU 限为 10 倍 |

---

### 示例应用前后对比

### 未设置资源的 Pod：

```yaml
spec:
  containers:
  - name: app
    image: nginx
```

→ 应用上面的 `LimitRange` 后，系统会自动加上：

```yaml
resources:
  requests:
    cpu: 200m
    memory: 256Mi
  limits:
    cpu: 500m
    memory: 512Mi
```

---