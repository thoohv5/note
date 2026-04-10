---
title: Istio 流量镜像 (Traffic Mirroring)
date: 2026-04-07
tags: [基础设施, K8s]
type: guide
status: complete
---

# Istio 流量镜像 (Traffic Mirroring)

# 概述

Istio 中的流量镜像（也称为影子流量）是一种强大的功能，允许您将生产流量复制到另一个服务，而不会影响实际的生产流量。这是进行服务测试和验证的理想方式。

- **镜像流量**：将实时流量的副本发送到镜像服务
- **不影响生产**：原始请求仍按正常路径处理，镜像请求是"发后即忘"的
- **用途**：常用于测试新版本服务、监控比较或压力测试

## **配置示例**

以下是一个典型的流量镜像 VirtualService 配置：

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: my-service
spec:
  hosts:
  - my-service.prod.svc.cluster.local
  http:
  - route:
    - destination:
        host: my-service.prod.svc.cluster.local
        subset: v1
      weight: 100
    mirror:
      host: my-service.prod.svc.cluster.local
      subset: v2
    mirror_percent: 100 # 镜像100%的流量  v1alpha3 版本叫 mirror_percent，在 v1beta1 以后改成 mirrorPercentage.value
```

## **关键配置参数**

1. **`mirror`**：指定镜像流量的目标服务
2. **`mirror_percent`**：控制镜像流量的百分比(0-100)
3. **`mirror_port`**：可指定不同的端口(默认与原始请求相同)

## **使用场景**

1. **版本验证**：将生产流量镜像到新版本，验证其行为
2. **性能测试**：用真实流量测试新服务的性能
3. **A/B测试**：比较不同版本的处理结果
4. **安全分析**：将流量镜像到安全分析工具

## **最佳实践**

1. **逐步增加镜像比例**：从少量流量开始(如10%)，逐步增加
2. **监控镜像服务**：确保镜像服务不会因负载而崩溃
3. **区分日志**：为镜像流量添加特殊标记以便区分
4. **资源隔离**：镜像服务应有独立的资源以避免影响生产

## **注意事项**

- 镜像流量会消耗额外的资源
- 响应不会被返回给原始客户端
- 某些有副作用的操作(如写数据库)可能需要特殊处理
- 需要仔细监控以避免意外影响