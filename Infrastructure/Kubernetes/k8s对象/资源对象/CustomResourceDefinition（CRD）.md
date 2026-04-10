---
title: CustomResourceDefinition（CRD）
date: 2026-04-07
tags: [基础设施, K8s]
type: reference
status: complete
---

# CustomResourceDefinition（CRD）

# 概述

CRD 的全称是 Custom Resource Definition。顾名思义，它指的就是，允许用户在 Kubernetes 中添加一个跟 Pod、Node 类似的、新的 API 资源类型，即：自定义 API 资源。

自定义 API 资源”实例，也叫 CR（Custom Resource）

```yaml
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: networks.samplecrd.k8s.io
spec:
  group: samplecrd.k8s.io
  version: v1
  names:
    kind: Network
    plural: networks
  scope: Namespaced
```