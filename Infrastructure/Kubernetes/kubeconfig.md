---
title: kubeconfig
date: 2026-04-07
  - 基础设施
  - K8s
type: guide
status: complete
---

## kubeconfig

## 概述

kubeconfig 文件是 Kubernetes 集群的配置文件，包含了访问集群所需的认证信息和连接细节。

## 配置

### **导出**

```bash
## 查看当前配置（不显示敏感信息）
kubectl config view

## 导出完整配置到文件（包含敏感信息）
kubectl config view --raw > kubeconfig.yaml
```

### 从 Master 节点导出

```bash
## 1. 直接复制管理员配置文件
sudo cp /etc/kubernetes/admin.conf ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config

## 2. 或者使用 kubectl 导出
kubectl config view --raw > kubeconfig.yaml
```

### **导入**

```bash
## 使用导出的配置文件
kubectl --kubeconfig=kubeconfig.conf get pods

## 或设置为默认配置
export KUBECONFIG=~/.kube/config
```

### 合并

```bash
export KUBECONFIG=~/.kube/config:~/path/to/kubeconfig.conf
kubectl config view --merge --flatten > ~/.kube/config_tmp
mv ~/.kube/config_tmp ~/.kube/config

KUBECONFIG=file1:file2:file3 kubectl config view --merge --flatten > ~/.kube/config
```

### kubeconfig文件详细

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: <base64编码的CA证书>
    server: https://<集群地址>:6443
  name: <集群名称>
contexts:
- context:
    cluster: <集群名称>
    user: <用户名称>
  name: <上下文名称>
current-context: <当前上下文名称>
kind: Config
users:
- name: <用户名称>
  user:
    client-certificate-data: <base64编码的客户端证书>
    client-key-data: <base64编码的客户端密钥>
```

## 切换工具

### 安装

```bash
## 安装 kubectx (包含 kubens)
## macOS
brew install kubectx
```

### 命令

```bash
## 使用
kubectx  # 列出所有上下文
kubectx <context-name>  # 切换到指定上下文
kubens   # 列出所有命名空间
kubens <namespace>  # 切换到指定命名空间
```