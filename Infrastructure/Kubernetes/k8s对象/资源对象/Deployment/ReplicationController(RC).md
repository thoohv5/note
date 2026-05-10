---
title: ReplicationController(RC)
date: 2026-04-07
tags: [基础设施, K8s, 对象]
type: reference
status: complete
---

## ReplicationController(RC)

## 概述

RC 是Kubernetes 系统中的核心概念之一，简单来说，它其实是定义了 一个期望的场景， 即声明某种Pod 的副本数量在任意时刻都符合某个预期值，所以RC 的定义包括如下几个部分。

- Pod 期待的副本数 (replicas)。
- 用于筛选目标Pod的LabelSelector。
- 当Pod的副本数量小于预期数量的时候，用于创建新Pod的Pod模板(template)。

## 实例

> mysql-rc.yaml
> 

```yaml
apiVersion: v1
kind: ReplicationController
metadata: 
	name: mysql
spec
	replicas: 1
	selector:
		app: mysql
	template:
		metadata: 
			labels:
				app: mysql
		spec:
			containers:
			- name: mysql
				image: mysql
				ports:
				- containerPort: 3306
				env:
				- name: MYSQI_ROOI_PASSWORD
					value: "123456"

```

### 命令

```docker
kubectl create -f mysql-rc.yaml

kubectl get rc

kubectl get pods

kubectl scale rc mysql-rc --replicas=3
```

## 扩展