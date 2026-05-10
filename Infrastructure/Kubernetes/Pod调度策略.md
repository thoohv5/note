---
title: Pod调度策略
date: 2026-04-07
tags:
  - 基础设施
  - K8s
type: guide
status: complete
---

## Pod调度策略

## 概述

在 Kubernetes 中，Pod 的调度策略决定了 **Pod 会被调度到哪个节点上运行**。K8s 提供了多种机制来控制这一行为，让你可以灵活地把 Pod 调度到满足你要求的节点上。

## 总览

| 策略类型 | 用途说明 |
| --- | --- |
| `resource limits` | 根据资源需求（CPU/内存）由调度器自动选择节点 |
| `nodeSelector` | 基于节点标签进行简单匹配 |
| `nodeAffinity` | 更复杂的标签匹配规则（亲和性） |
| `taints` & `tolerations` | 控制哪些 Pod 可容忍节点的污点（Taint） |
| `podAffinity` | 与某些 Pod 同节点（亲和性） |
| `podAntiAffinity` | 与某些 Pod 不同节点（反亲和性） |

### 两种类型Affinity

| 类型 | 字段名 | 是否强制 | 说明 |
| --- | --- | --- | --- |
| **必需匹配（Required）** | `requiredDuringSchedulingIgnoredDuringExecution` | ✅ 强制 | 必须匹配规则，否则 Pod 无法被调度 |
| **优先匹配（Preferred）** | `preferredDuringSchedulingIgnoredDuringExecution` | ❌ 非强制 | 尽量满足规则，实在不满足也会调度 |

### 节点标签

```yaml
## 找到你要访问的那个 Pod，打一个唯一标签
kubectl label node <pod-name> scope=demo

## 查看标签
kubectl get node --show-labels

## 移除标签
kubectl label node <node-name> scope-
```

## 策略类型

### 使用资源请求控制调度

```yaml
resources:
  requests:
    memory: "512Mi"
    cpu: "1"
```

> 调度器会只选择有足够资源的节点进行调度。
> 

---

### `nodeSelector`

```yaml
spec:
  nodeSelector:
    scope: demo
```

> Pod 只能被调度到带有 scope=demo 标签的节点上。
> 

---

### `nodeAffinity`

> 可以使用 In、NotIn、Exists 等复杂表达式控制调度行为。
> 

```yaml
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: scope
                operator: In
                values:
                  - demo
```

---

### `taints/tolerations`

在 Kubernetes 中，**`taints`**和**`tolerations`**  是一对用于控制 Pod 和节点调度的机制。它们一起工作，用于确保某些 Pod 只能调度到某些特定的节点，或者某些节点只能接收特定的 Pod。

### `taints` 污点

**`taints`** 是应用于节点的，它用于标记某些节点，表明它们可能不适合某些 Pod。在节点上设置污点后，只有具有对应的容忍（toleration）的 Pod 才能被调度到该节点。

**Taints 的结构**：

- **key**：污点的键
- **value**：污点的值
- **effect**：污点的影响，可以是以下几种：
    - `NoSchedule`：如果 Pod 没有容忍该污点，就不能被调度到这个节点。
    - `PreferNoSchedule`：如果 Pod 没有容忍该污点，Kubernetes 会尽量避免将 Pod 调度到这个节点。
    - `NoExecute`：如果 Pod 没有容忍该污点，不仅不能调度到该节点，还会被驱逐（Eviction）。
- 示例YAML
    
    ```yaml
    spec:
      tolerations:
        - key: "dedicated"
          operator: "Equal"
          value: "special"
          effect: "NoSchedule"
    ```
    

### `tolerations` 容忍污点

**`tolerations`** 是 Pod 的一部分，定义了 Pod 可以容忍哪些污点，允许它们被调度到标记了这些污点的节点上。

- **作用**：`tolerations` 用来声明一个 Pod 可以接受哪些污点，进而调度到这些节点上。

**Tolerations 的结构**：

- **key**：污点的键
- **value**：污点的值
- **operator**：污点的操作符，可以是：
    - `Equal`：表示 `key` 和 `value` 必须精确匹配。
    - `Exists`：表示只需要匹配 `key`，不关心 `value`。
- **effect**：表示 Pod 允许哪些类型的污点。
    - `NoSchedule`：Pod 可以容忍 `NoSchedule` 类型的污点。
    - `PreferNoSchedule`：Pod 可以容忍 `PreferNoSchedule` 类型的污点。
    - `NoExecute`：Pod 可以容忍 `NoExecute` 类型的污点。

### 示例YAML

```yaml
spec:
  tolerations:
  - key: "scope"
    operator: "Equal"
    value: "demo"
    effect: "NoSchedule"
```

**查看节点标签和污点**

```bash
kubectl get nodes --show-labels
kubectl describe node <node-name> | grep -i taints
```

### `podAffinity/podAntiAffinity`

### `podAffinity`与某类 Pod 同节点

- 示例YAML
    
    ```yaml
    spec:
      affinity:
        podAffinity: # 与标签app=frontend的pod在同一节点
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchLabels:
                  app: frontend
              topologyKey: "kubernetes.io/hostname"
    ```
    

### `podAntiAffinity` 与某类 Pod 不同节点

- 示例YAML
    
    ```yaml
    spec:
      affinity:
        podAntiAffinity: # 与标签app=frontend的pod不能在同一节点
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchLabels:
                  app: frontend
              topologyKey: "kubernetes.io/hostname"
    ```
    

在 podAffinity / podAntiAffinity 中，必须指定 `topologyKey`：
比如：

- `kubernetes.io/hostname`：限制在不同主机上
- `topology.kubernetes.io/zone`：跨可用区调度