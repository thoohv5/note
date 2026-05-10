---
title: ReplicaSet(RS)
date: 2026-04-07
tags:
  - 基础设施
  - K8s
type: reference
status: complete
---

## ReplicaSet(RS)

## 概述

`ReplicaSet (RS)` 是 Kubernetes 中用来 维持一组 Pod 副本始终处于期望状态 的控制器。它是 `ReplicationController (RC)` 的升级版，并且是 `Deployment` 的核心组成部分 —— 每次你创建一个 Deployment，其实底层就是由一个或多个 ReplicaSet 托管 Pod 的。

### 示例

```yaml
apiversion: extensions/v1betal 
kind: ReplicaSet
metadata:
    name: frontend
spec:
    selector: 
        matchLabels:
            tier: frontend 
        matchexpressions:
            - {key: tier, operator: In, values: [frontend])
```

### ✅ `matchLabels` 是简单的等值匹配（key = value）

```yaml
selector:
  matchLabels:
    app: nginx
    env: prod

```

> 上面的意思是选择那些标签中同时包含 app=nginx 且 env=prod 的对象。
> 

---

### ✅ `matchExpressions` 是更灵活的表达式匹配

```yaml
selector:
  matchExpressions:
    - { key: app, operator: In, values: [nginx, web] }
    - { key: tier, operator: NotIn, values: [dev] }
    - { key: release, operator: Exists }

```

| Operator | 说明 |
| --- | --- |
| `In` | 匹配值在给定列表中 |
| `NotIn` | 匹配值不在列表中 |
| `Exists` | 只要包含该 key 即匹配 |
| `DoesNotExist` | 不包含该 key 即匹配 |

---

### 🧠 两者可以同时使用

```yaml
selector:
  matchLabels:
    env: prod
  matchExpressions:
    - key: app
      operator: In
      values: [nginx, web]

```

表示筛选出：

`env=prod` 且 `app` 的值是 `nginx` 或 `web` 的对象。