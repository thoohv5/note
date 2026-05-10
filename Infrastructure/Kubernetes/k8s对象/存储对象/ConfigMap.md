---
title: ConfigMap
date: 2026-04-07
tags: [基础设施, K8s, 对象]
type: guide
status: complete
---

## ConfigMap

## 概述

在 Kubernetes 中，`ConfigMap` 是一种用于**存储非机密的配置数据**的对象，主要用于将配置从代码中解耦，让应用部署更加灵活。

## 创建

### 通过 YAML 文件创建【优先】

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
  namespace: default
data:
  APP_MODE: "debug"
  APP_PORT: "8080"
  config.yaml: |
    log-level: info
    max-conn: 100
```

### 通过命令行创建

```bash
## 创建 ConfigMap（从命令行）
kubectl create configmap my-config --from-literal=APP_PORT=8080

## 创建（从文件）
kubectl create configmap my-config --from-file=config.yaml
```

## 使用

### 作为环境变量

```yaml
envFrom:
- configMapRef:
    name: my-config
```

### 作为挂载文件【优先】

```yaml
volumeMounts:
- name: config-volume
  mountPath: /etc/config

volumes:
- name: config-volume
  configMap:
    name: my-config
```

<aside>
💡

ConfigMap 的每个键将变成一个文件 `/etc/config/<key>`。

</aside>

### Pod容器中未更新的情况

1. 以 **volume 挂载方式使用 ConfigMap**，有更新延迟： 在 Kubernetes 默认行为下，投影 volume 会每 ~1 分钟左右刷新一次（轮询检测）
2. 使用了 `subPath` 挂载
    
    ```yaml
    volumeMounts:
      - name: config-volume
        mountPath: /etc/config/my-config.txt
        subPath: my-config.txt
    ```