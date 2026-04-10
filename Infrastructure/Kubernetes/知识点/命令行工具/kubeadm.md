# kubeadm

# 概述

kubeadm：集群初始化工具

> 作用：用于初始化 Kubernetes 集群（master 节点 + worker 加入）
> 
- 简化了集群的部署流程
- 不负责运行集群，只是帮你“部署/配置”
- 常用命令：
    - `kubeadm init`：初始化 master 节点
    - `kubeadm join`：worker 节点加入集群
    - `kubeadm reset`：重置集群配置

📦 **它配置的内容包括**：

- 生成 TLS 证书、kubeconfig
- 启动 control plane 的组件（apiserver, controller-manager, scheduler 等）
- 设置网络配置（需要你手动装 CNI，如 flannel 或 calico）

# 命令

## `kubeadm init`

```bash
kubeadm init --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.23.6 --pod-network-cidr=10.244.0.0/16
```

## `kubeadm token`

```bash
# 查看
kubeadm token list

# 重新获取
kubeadm token create --print-join-command
```

## `kubeadm join`

```bash
# 添加节点
kubeadm join <master-ip>:<master-port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

## `kubeadm certs`

```bash
# 检测证书
kubeadm certs check-expiration

# 更新证书
kubeadm certs renew all
```

# 附录

[使用 kubeadm 引导集群](https://kubernetes.io/zh-cn/docs/setup/production-environment/tools/kubeadm/)