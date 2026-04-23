---
title: Role/RoleBinding
date: 2026-04-07
  - 基础设施
  - K8s
type: reference
status: complete
---

## Role/RoleBinding

## **Role**

作用于命名命名空间。

- 示例：
    
    ```yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: Role
    metadata:
      namespace: default
      name: pod-reader
    rules:
    - apiGroups: [""]       # 核心API组（如Pod）
      resources: ["pods"]
      verbs: ["get", "watch", "list"]
    - apiGroups: ["apps"]   # 扩展API组（如 Deployment）
    	resources: ["deployments"]
    	verbs: ["*"]          # 通配符表示所有操作
    ```
    

## **RoleBinding**

把 Role 绑定给用户或服务账户。

- 示例：
    
    ```yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      name: read-pods
      namespace: default
    subjects:
    - kind: User
      name: thooh
      apiGroup: rbac.authorization.k8s.io
    roleRef:
      kind: Role
      name: pod-reader
      apiGroup: rbac.authorization.k8s.io
    ```
    

## 查看

```bash
## 查看所有 RoleBinding
kubectl get rolebindings --all-namespaces

## 查看 RoleBinding 详情
kubectl describe rolebinding <binding-name> -n <namespace>

## 查看 Role 权限
kubectl describe role <role-name> -n <namespace>
```

## 实验

- YAML示例
    
    ```yaml
    # sa-role.yaml
    apiVersion: v1
    kind: Namespace
    metadata:
      name: demo
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      namespace: demo
      name: demo-sa
    ---
    apiVersion: v1
    kind: Secret
    metadata:
      namespace: demo
      name: demo-sa-token
      annotations:
        kubernetes.io/service-account.name: demo-sa
    type: kubernetes.io/service-account-token
    
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: Role
    metadata:
      name: pod-reader
      namespace: demo
    rules:
      - apiGroups: [""]
        resources: ["pods"]
        verbs: ["get", "list", "watch"]
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      name: read-pods-binding
      namespace: demo
    subjects:
      - kind: ServiceAccount
        name: demo-sa
        namespace: demo
    roleRef:
      kind: Role
      name: pod-reader
      apiGroup: rbac.authorization.k8s.io
    ```
    

- 脚本`sa-kubeconfig.sh`
    
    ```bash
    #!/bin/local/env bash
    
    #!/bin/bash
    
    set -e
    
    SECRET_NAME="demo-sa-token"
    NAMESPACE="demo"
    SA_NAME="demo-sa"
    KUBECONFIG_FILE="sa-kubeconfig"
    
    # 获取 Token 和 CA 证书
    TOKEN=$(kubectl get secret $SECRET_NAME -n $NAMESPACE -o jsonpath='{.data.token}' | base64 -d)
    CA_CERT=$(kubectl get secret $SECRET_NAME -n $NAMESPACE -o jsonpath='{.data.ca\.crt}')
    CLUSTER_NAME=$(kubectl config view --minify -o jsonpath='{.clusters[0].name}')
    SERVER=$(kubectl config view --minify -o jsonpath='{.clusters[0].cluster.server}')
    
    # 生成 kubeconfig
    cat <<EOF > $KUBECONFIG_FILE
    apiVersion: v1
    kind: Config
    clusters:
    - cluster:
        certificate-authority-data: $CA_CERT
        server: $SERVER
      name: $CLUSTER_NAME
    contexts:
    - context:
        cluster: $CLUSTER_NAME
        user: $SA_NAME
        namespace: $NAMESPACE
      name: ${NAMESPACE}-context
    current-context: ${NAMESPACE}-context
    users:
    - name: $SA_NAME
      user:
        token: $TOKEN
    EOF
    
    ```
    

- 验证
    
    ```bash
    # 允许操作
    kubectl --kubeconfig=sa-kubeconfig get pods -n demo
    
    # 不允许操作
    kubectl --kubeconfig=sa-kubeconfig create deployment nginx --image=nginx -n demo
    ```