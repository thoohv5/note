---
title: Namespace (命名空间)
date: 2026-04-07
tags: [基础设施, K8s]
type: reference
status: incomplete
---

# Namespace (命名空间)

# 概述

`Namespace(命名空间)`是Kubernetes系统中的另一个非常重要的概念，Namespace在很多情况下用于实现多租户的资源隔离。Namespace通过将集群内部的资源对象“分配”到不同的Namespace中，形成逻辑上分组的不同项目、小组或用户组，便于不同的分组在共享使用整个集群的资源的同时还能被分别管理。

Kubernetes集群在启动后，会创建一个名为“default”的Namespace