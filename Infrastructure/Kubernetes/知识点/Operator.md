---
title: Operator
date: 2026-04-07
tags: [基础设施, K8s]
type: reference
status: complete
---

# Operator

# 概述

**Operator 是一种用于自动化管理 Kubernetes 应用程序的控制器，结合了业务逻辑和 Kubernetes 原生机制。**

一句话理解：

> Operator = 自定义控制器 + 自定义资源（CRD）它能像 K8s 的原生命令一样“理解你的业务应用”，并做出智能运维操作。
> 

# 使用场景

- 自动创建数据库副本（如：MongoDB Operator）•
- 自动故障迁移 / 恢复• 自动扩容 / 缩容• 定时备份 / 升级
- 一键安装复杂系统（Kafka、Redis、Elastic）

# 核心构成

| 组件 | 作用 |
| --- | --- |
| CRD（CustomResourceDefinition） | 自定义资源类型，比如 `PostgresCluster` |
| Controller | 监听资源变动并自动执行相应逻辑 |
| Reconcile Loop | 核心逻辑：当前状态 ≠ 期望状态 → 修正它 |

# Operator 框架（Operator SDK）

官方推荐使用 Operator SDK 来快速开发 Operator，支持：

- Go（最主流）
- Ansible（适合脚本驱动）
- Helm（适合已有 Helm chart）

# 实操

### **第 1 步：安装 Operator SDK**

```bash
brew install operator-sdk
```

---

### **第 2 步：初始化项目**

```bash
operator-sdk init --domain=mydomain.com --repo=github.com/my/app-operator
```

---

### **第 3 步：创建 API 和 Controller**

```bash
operator-sdk create api --group=web --version=v1 --kind=WebApp --resource --controller
```

生成：

- CRD：`WebApp` 自定义资源
- 控制器：监听 WebApp 的变化，自动处理逻辑

---

### **第 4 步：定义 WebApp 结构**

编辑 `api/v1/webapp_types.go`：

```go
type WebAppSpec struct {
  Replicas int    `json:"replicas"`
  Image    string `json:"image"`
}

type WebAppStatus struct {
  AvailableReplicas int `json:"availableReplicas"`
}
```

---

### **第 5 步：编写控制逻辑（核心 Reconcile）**

编辑 `controllers/webapp_controller.go`：

```go
func (r *WebAppReconciler) Reconcile(ctx context.Context, req ctrl.Request) (ctrl.Result, error) {
var webApp webappv1.WebApp
if err := r.Get(ctx, req.NamespacedName, &webApp); err != nil {
    return ctrl.Result{}, client.IgnoreNotFound(err)
  }

// 创建或更新 Deployment 来部署 WebApp
  deploy := &appsv1.Deployment{
    // 定义 Deployment 结构...
  }

  err := r.Create(ctx, deploy)
if err != nil && !apierrors.IsAlreadyExists(err) {
    return ctrl.Result{}, err
  }

return ctrl.Result{}, nil
}
```

---

### **第 6 步：运行并测试 Operator**

```bash
make install    # 安装 CRD
make run        # 本地运行 Operator
```

创建一个 WebApp 资源对象：

```bash
apiVersion: web.mydomain.com/v1
kind: WebApp
metadata:
  name: my-web
spec:
  replicas: 2
  image: nginx
```