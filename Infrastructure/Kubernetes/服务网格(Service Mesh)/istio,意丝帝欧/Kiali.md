---
title: Kiali
date: 2026-04-07
tags: [基础设施, K8s]
type: note
status: complete
---

# Kiali

## **添加 Kiali Helm 仓库**

```bash
helm repo add kiali https://kiali.org/helm-charts
helm repo update
```

---

## **安装 Kiali**

### **基础安装（默认配置）**

```bash
helm install kiali-server kiali/kiali-server \
  --namespace istio-system \
  --set auth.strategy="anonymous" # 测试环境可设为匿名访问
```

### **生产环境推荐配置**

```bash
helm install kiali-server kiali/kiali-server \
  --namespace istio-system \
  --set auth.strategy="token" \# 使用 Token 认证--set external_services.prometheus.url="http://prometheus.istio-system:9090" \
  --set external_services.grafana.url="http://grafana.istio-system:3000" \
  --set persistence.enabled=true \# 启用持久化存储--set persistence.storageClass="standard"# 指定 StorageClass
```

---

## **验证安装**

```bash
# 检查 Pod 状态
kubectl get pods -n istio-system -l app.kubernetes.io/name=kiali

# 检查 Service
kubectl describe svc kiali -n istio-system  # 或 kiali-server
```

---

## **访问 Kiali Dashboard**

### **临时访问（Port-Forward）**

```bash
kubectl port-forward svc/kiali -n istio-system 20001:20001
```

访问 → [http://localhost:20001](http://localhost:20001/)

### **生产环境访问（通过 Ingress）**

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: kiali-vs
  namespace: istio-system
spec:
  hosts:
  - "kiali.your-domain.com"# 替换为你的域名gateways:
  - istio-system/istio-ingressgateway
  http:
  - route:
    - destination:
        host: kiali-server.istio-system.svc.cluster.local
        port:
          number: 20001
```

需配套创建 DNS 记录或修改本地 **`hosts`** 文件。

---

## **升级/卸载**

### **升级 Kiali**

```bash
helm upgrade kiali-server kiali/kiali-server -n istio-system --reuse-values
```

### **卸载 Kiali**

```bash
helm uninstall kiali-server -n istio-system
```