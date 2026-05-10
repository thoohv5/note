---
title: minikube
date: 2026-04-07
tags: [基础设施, K8s, 安装]
type: guide
status: complete
---

## minikube

### 安装

```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-darwin-amd64
sudo install minikube-darwin-amd64 /usr/local/bin/minikube

minikube delete
minikube delete --all

echo 'source <(minikube completion bash)' >> ~/.bashrc # 添加命令补全
source ~/.bashrc

minikube node list

## 服务转发
minikube service demo-svc --url
```

### 卸载

```bash
minikube delete &&
docker stop $(docker ps -aq) &&
rm -rf ~/.kube ~/.minikube &&
sudo rm -rf /usr/local/bin/localkube /usr/local/bin/minikube &&
launchctl stop '*kubelet*.mount' &&
launchctl stop localkube.service &&
launchctl disable localkube.service &&
sudo rm -rf /etc/kubernetes/ && 
docker system prune -af --volumes
```

### 启动

```bash
minikube start
minikube start --image-mirror-country='cn'

minikube status

minikube stop
```

### 查看

```bash
## 查看集群的所有资源
kubectl get all

## 进入节点服务器
minikube ssh
```

### 可视化界面

```bash

## 开启界面
minikube dashboard

## 代理
kubectl proxy --port=8001 --address='192.168.31.130' --accept-hosts='^.*'

```

### 地址

[http://192.168.31.130:8001/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/#/workloads?namespace=default](http://192.168.31.130:8001/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/#/workloads?namespace=default)

### 插件

```bash
minikube addons list

minikube addons enable ingress
```

### ingress

```yaml

minikube tunnel

ps -afe | grep -i minikube

minikube tunnel --cleanup
```

### `kubectl`

```bash
minikube kubectl
```

### 问题

> Exiting due to TUNNEL_ALREADY_RUNNING: Another tunnel process is already running, terminate the existing instance to start a new one
>