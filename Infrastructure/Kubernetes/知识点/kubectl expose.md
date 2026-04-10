---
title: kubectl expose
date: 2026-04-07
tags: [基础设施, K8s]
type: reference
status: complete
---

# kubectl expose

# 概述

**kubectl expose 命令用于将 Kubernetes 中的资源（如 Pod、Deployment、ReplicaSet 等）暴露为一个 Service，从而使其能够通过网络进行访问，该命令可创建不同类型的 Service，例如 ClusterIP、NodePort 和 LoadBalancer，一起来看看如何使用吧！**

## **场景**

- **将 Pod 或 Deployment 暴露为 Service：使用 kubectl expose 可方便地对内部服务进行外部访问，使其他服务或用户可以访问。**
- **开发和测试：在开发和测试环境中快速暴露新创建的应用程序，以便进行验证**
- **临时访问：为某些需要临时公开的服务（例如调试或性能测试）创建 Service**

# **语法**

```bash
# RESOURCE_TYPE: 需要暴露的资源类型，例如 pod、deployment
# RESOURCE_NAME: 需要暴露的资源名称
# --port: Service 的端口，外部请求将通过此端口访问服务
# --target-port: 实际运行在容器内的应用程序端口
# OPTIONS: 附加选项，如指定 Service 类型等
kubectl expose [RESOURCE_TYPE] [RESOURCE_NAME] --port=[PORT] --target-port=[TARGET_PORT] [OPTIONS]
```

## 示例

```bash
# 查看帮助命令
kubectl expose --help
 
# 暴露 Deployment->my_deploy，为NodePort类型的Service
kubectl expose deploy my_deploy --type=NodePort --port=80 --target-port=8080
 
# 暴露 Pod->my_pod, 为ClusterIP类型的Service
kubectl expose pod my_pod --type=ClusterIP --port=80 --target-port=8080 
 
# 暴露 ReplicaSet->my_rs，为负载均衡的Service
kubectl expose rs my_rs --type=LoadBalancer --port=9090 --target-port=8000

```