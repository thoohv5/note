---
title: NGINX Gateway Ingress
date: 2026-04-07
tags: [基础设施, K8s]
type: guide
status: complete
---

# NGINX Gateway Ingress

### 安装

```bash
kubectl create namespace ingress-nginx

# 为裸机 / kubeadm / 无云 LB 环境准备的 NodePort 模式
wget -O ingress-nginx.yaml https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.9.4/deploy/static/provider/baremetal/deploy.yaml

# 3. 替换镜像为国内源（阿里云）
sed -i 's|registry.k8s.io/ingress-nginx/controller:[^[:space:]]*|registry.aliyuncs.com/google_containers/nginx-ingress-controller:v1.9.4|g' ingress-nginx.yaml
sed -i 's|registry.k8s.io/ingress-nginx/kube-webhook-certgen:[^[:space:]]*|registry.aliyuncs.com/google_containers/kube-webhook-certgen:v20231011-8b53cabe0|g' ingress-nginx.yaml

kubectl apply -f ingress-nginx.yaml

watch kubectl get pods -n ingress-nginx
```

### 查看

```yaml
kubectl get svc ingress-nginx-controller -n ingress-nginx
```

### 示例

```bash
---
# 创建命名空间
apiVersion: v1
kind: Namespace
metadata:
  name: pro

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-interface-deploy
  namespace: pro # 声明工作空间，默认为default
  labels:
    name: demo
    type: interface
spec:
  replicas: 2
  selector:
    matchLabels:
      name: demo-interface
  template:
    metadata:
      labels:
        name: demo-interface
    spec:
      containers:
        - name: demo-container
          image: crpi-n1cav84krirf898d.cn-chengdu.personal.cr.aliyuncs.com/thoohv5/demo-interface:v0.0.2
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 8080
      imagePullSecrets:
        - name: aliyun-registry

---
apiVersion: v1
kind: Service
metadata:
  name: demo-interface-svc
  namespace: pro # 声明工作空间，默认为default
spec:
  type: NodePort
  ports:
    - name: http
      port: 18080 # Service暴露在cluster-ip上的端口，通过<cluster-ip>:port访问服务,通过此端口集群内的服务可以相互访问
      targetPort: 8080 # Pod的外部访问端口，port和nodePort的数据通过这个端口进入到Pod内部，Pod里面的containers的端口映射到这个端口，提供服务
      nodePort: 31080 # Node节点的端口，<nodeIP>:nodePort 是提供给集群外部客户访问service的入口
  selector:
    name: demo-interface

---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-interface-ingress
  namespace: pro
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
    - host: demo-interface.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: demo-interface-svc
                port:
                  number: 18080

# curl -H "Host: demo-interface.local" http://192.168.31.100:31982
```