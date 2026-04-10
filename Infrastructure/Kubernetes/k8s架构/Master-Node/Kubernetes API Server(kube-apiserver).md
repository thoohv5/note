---
title: Kubernetes API Server(kube-apiserver)
date: 2026-04-07
tags: [基础设施, K8s]
type: reference
status: complete
---

# Kubernetes API Server(kube-apiserver)

# 概述

`Kubernetes API Server`通过一个名为`kube-apiserver`的进程提供服务，该进程运行在Master节点上。在默认情况下，kube-apiserver进程在本机的**8080**端又(对应参数`--insecure-port`)提供`REST`服务。我们可以同时启动HTTPS安全端又(`--secure-port=**6443**`)来启动安全机制，加强`RESTAPI`访问的安全性。

通常我们可以通过命令行 工具kubectl 来与Kubernetes API Server 交互，它们之间的接又是 REST 调用。

### 版本信息

### 所支持的资源对象的种类

### 返回集群中的Pod列表

### 返回集群中的Service列表

### 返回集群中的Deployment列表

控制器的类别

- `Replication Controller`
- `Node controller`
- `ResourceQuota Controller`
- `Namespace Controller`
- `ServiceAccount Controller`
- `Token Controller`
- `Service Controller`
- `Endpoints Controller`