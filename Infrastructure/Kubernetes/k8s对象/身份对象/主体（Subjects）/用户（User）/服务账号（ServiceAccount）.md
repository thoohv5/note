---
title: 服务账号（ServiceAccount）
date: 2026-04-07
  - 基础设施
  - K8s
type: reference
status: complete
---

## 服务账号（ServiceAccount）

在 Kubernetes 中，`ServiceAccount` 是 Pod 在访问集群 API 时使用的身份。你可以使用它来控制 Pod 的权限（配合 `Role`/`ClusterRole` 和绑定）。

- 示例
    
    ```yaml
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: demo-sa
      namespace: demo
    ```
    

等价于：

```yaml
- kind: User
  name: system:serviceaccount:demo:demo-sa
```

## 查看

```bash
## 所有命名空间的 ServiceAccount
kubectl get serviceaccounts --all-namespaces

## 指定命名空间的 ServiceAccount
kubectl get serviceaccounts -n <namespace>

## 详情
kubectl describe serviceaccount <serviceaccount-name> -n <namespace>

## Pod 绑定 ServiceAccount
kubectl get pod <pod-name> -n <namespace> -o jsonpath='{.spec.serviceAccountName}'

```