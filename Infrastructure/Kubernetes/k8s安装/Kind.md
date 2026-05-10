---
title: Kind
date: 2026-04-07
tags: [基础设施, K8s, 安装]
type: guide
status: complete
---

## Kind

## 安装

```bash
brew install kubectl

brew install kind
```

### 创建集群

```yaml
## 创建集群
kind create cluster --name demo

## 集群信息
kubectl cluster-info --context kind-demo

## 删除集群
kind delete cluster --name demo
```

### 安装istio

```yaml
istioctl install --set profile=default -y
kubectl label namespace default istio-injection=enabled
```

### Ingress

```yaml
## 支持 Ingress 控制器的集群
cat <<EOF | kind create cluster --config=-
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
EOF
```

## 附录

[使用 kind 在本地运行 k8s - MaxBruce - 博客园](https://www.cnblogs.com/bruce1992/p/17782928.html)