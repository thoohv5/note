---
title: Bookinfo Demo
date: 2026-04-07
tags: [基础设施, K8s]
type: guide
status: complete
---

# Bookinfo Demo

## 部署

```bash
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.27/samples/bookinfo/platform/kube/bookinfo.yaml

kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.27/samples/bookinfo/networking/bookinfo-gateway.yaml

```

## 访问

```bash
# 查看端口
kubectl get svc istio-ingressgateway -n istio-system

# 查看IP
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' demo-control-plane
```

| 场景 | 访问方式 |
| --- | --- |
| **宿主机 + NodePort** | `http://localhost:31516/productpage` |
| **宿主机 + port-forward** | `kubectl -n istio-system port-forward svc/istio-ingressgateway 8080:80` 然后 `http://localhost:8080/productpage` |
| **集群内 Pod** | `curl http://istio-ingressgateway.istio-system.svc.cluster.local/productpage` |