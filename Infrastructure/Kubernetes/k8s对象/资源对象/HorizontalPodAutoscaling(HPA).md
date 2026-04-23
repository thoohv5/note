---
title: HorizontalPodAutoscaling(HPA)
date: 2026-04-07
  - 基础设施
  - K8s
type: reference
status: complete
---

## HorizontalPodAutoscaling(HPA)

## 概述

`HorizontalPodAutoscaling`简称`HPA`，意思是**Pod横向自动扩容**，与之前的`RC`、`Deployment`一样，也属于一种`Kubernetes`资源对象。通过追踪分析RC控制的所有目标Pod的负载变化情况，来确定是否需要针对性地调整目标Pod的副本数，这是HIPA的实现原理。当前，HPA可以有以下两种方式作为Pod负载的度量指标。

## 度量指标

### `CPUUtilizationPercentage`

`CPUUtilizationPercentage`是一个算术平均值，即目标Pod所有副本自身的CPU利用率的平均值。一个Pod自身的CPU利用率是该Pod当前CPU的使用量除以它的PodRequest的值，比如我们定义一个Pod的PodRequest为0.4，而当前Pod的CPU使用量为0.2，则它的CPU使用率为50%，如此一来，我们就可以就算出来一个RC控制的所有Pod副本的CPU利用率的算术平均值了。如果某一时刻CPUUtilizationPercentage的值超过80%，则意味着当前的Pod副本数很可能不足以支撑接下来更多的请求，需要进行动态扩容，而当请求高峰时段过去后，Pod的CPU利用率又会降下来，此时对应的Pod副本数应该自动减少到一个合理的水平。

`CPUUtilizationPercentage`计算过程中使用到的Pod的CPU使用量通常是1分钟内的平均值。

如果目标Pod没有定义PodRequest的值，则无法使用CPUUtilizationPercentage*实现Pod横向自动扩容的能力。

- 示例YAML
    
    ```yaml
    apiVersion: autoscaling/v2
    kind: HorizontalPodAutoscaler
    metadata:
      name: trade-mq-hpa
    spec:
      maxReplicas: 5
      metrics:
        - resource:
            name: cpu
            target:
              averageUtilization: 70
              type: Utilization
          type: Resource
      minReplicas: 1
      scaleTargetRef:
        apiVersion: apps/v1
        kind: Deployment
        name: trade-mq-v1-pro
      behavior:  # 以下配置为默认配置，您可以按需自定义字段。
        scaleDown: # 通过此字段配置自定义的缩容行为。
          stabilizationWindowSeconds: 300
          policies: 
          - type: Pods
            value: 1
            periodSeconds: 15
        scaleUp:  # 通过此字段配置自定义的扩容行为。
          stabilizationWindowSeconds: 0
          policies:
          - type: Pods
            value: 2
            periodSeconds: 15
    ```
    

### 手动创建

```yaml
kubectl autoscale deployment php-apache--cpu-percent=90- min=1--max=10
```

### `应用程序自定义的度量指标，比如服务在每秒内的相应的请求数 (TPS 或QPS)`