---
title: k8s测试工具
date: 2026-04-07
tags:
  - 基础设施
  - K8s
type: note
status: complete
---

## k8s测试工具

### **1. 全能型排查工具箱(已成功)**

**镜像名称**: **`nicolaka/netshoot`**

```bash
kubectl run tmp-shell --rm -it --image nicolaka/netshoot -- /bin/bash
```

**包含工具**:

- 网络工具：ping, traceroute, tcpdump, netstat, ss, iftop
- DNS工具：dig, nslookup
- HTTP工具：curl, httpie
- 其他：jq, yq, vim, tmux

### **2. 网络诊断专用**

**镜像名称**: **`alpine/socat`**

```bash
kubectl run tmp-socat --rm -it --image alpine/socat -- sh
```

**用途**:

- 端口转发测试
- TCP/UDP 连接验证
- 网络代理调试

### **3. 存储系统排查**

**镜像名称**: **`bitnami/kubectl`**

```bash
kubectl run test-kubectl --rm -it --image bitnami/kubectl -- bash
```

**用途**:

- PVC/PV 问题排查
- 存储类检查
- 访问集群内部资源

### **4. 性能分析工具**

**镜像名称**: **`sysdig/sysdig`**

```bash
kubectl run test-sysdig --rm -it --image sysdig/sysdig -- bash
```

**工具**:

- sysdig 系统调用分析
- csysdig 交互式监控
- 容器性能分析

### **5. 安全审计工具**

**镜像名称**: **`aquasec/kube-bench`**

```bash
kubectl run test-bench --rm -it --image aquasec/kube-bench -- bash
```

**用途**:

- CIS Kubernetes 基准测试
- 安全配置检查
- 合规性审计

### **6. 集群状态诊断**

**镜像名称**: **`bitnami/kube-state-metrics`**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kube-state-metrics
spec:
  template:
    spec:
      containers:
      - name: kube-state-metrics
        image: bitnami/kube-state-metrics:latest
```

**用途**:

- 集群资源状态监控
- 对象计数统计
- 资源分配分析

### **7. 网络策略验证**

**镜像名称**: **`network-multitool`**

```bash
kubectl run test-net-tool --rm -it --image praqma/network-multitool -- bash
```

**包含工具**:

- curl, wget
- ping, traceroute
- nslookup, dig
- telnet, netcat