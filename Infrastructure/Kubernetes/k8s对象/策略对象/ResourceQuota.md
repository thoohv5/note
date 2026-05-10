---
title: ResourceQuota
date: 2026-04-07
tags:
  - 基础设施
  - K8s
type: reference
status: complete
---

## ResourceQuota

## 概述

ResourceQuota 是 Kubernetes 用来**限制命名空间内资源使用上限**的一种机制。

## 命令

```yaml
kubectl get resourcequota -n dev

kubectl describe quota dev-quota -n dev
```

## 示例

### 限制命令空间

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
	name: dev-quota
	namespace: dev
spec:
hard:
    pods:"20"
    requests.cpu:"4"
    requests.memory:8Gi
    limits.cpu:"10"
    limits.memory:16Gi
    persistentvolumeclaims: "5"
```

📌 含义解读：

- Pod 数不能超过 20 个•
- 所有 Pod 的 CPU 请求总和 ≤ 4 核•
- 所有 Pod 的内存限制总和 ≤ 16Gi•
- PVC 不得超过 5 个