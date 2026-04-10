# ClusterRole/ClusterRoleBinding

# **`ClusterRole`**

集群级别的权限，允许用户跨所有命名空间（或特定命名空间）访问 Kubernetes 资源。它是一个在整个集群范围内有效的角色，可以用于控制访问集群中的资源（如节点、命名空间、Pod 等）。

### 内置`ClusterRole`

```markdown
|ClusterRole | 名称权限范围|
| -- | -- |
| view | 只读权限，可查看命名空间内大多数资源（不包括 Secrets）。|
| edit | 允许修改资源（如创建/删除 Pod、更新 Deployment），但不能管理 RBAC 或命名空间。|
| admin | 命名空间管理员，可管理资源（包括 Role 和 RoleBinding），但不能操作资源配额。|
| cluster-admin | 集群超级管理员，拥有所有资源的完全控制权。|
```

- 示例
    
    ```yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
    metadata:
      # 角色的名称
      name: pod-viewer
    rules:
    - apiGroups: [""]
      # 资源类型
      resources: ["pods"]
      verbs: ["get", "list", "watch"]
    ```
    

---

# **`ClusterRoleBinding`**

将 **ClusterRole** 分配给某个用户、组或服务账户的对象。它允许某个身份（用户、服务账户等）在集群范围内或特定命名空间内拥有 ClusterRole 中定义的权限。

- 示例
    
    ```yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
      # 绑定的名称
      name: orbstack-viewer
    subjects:
    - kind: User
      # 用户名称（可以是用户、服务账户等）
      name: orbstack
      apiGroup: rbac.authorization.k8s.io
    roleRef:
      kind: ClusterRole
      # 被绑定的角色（这里是 pod-viewer）
      name: pod-viewer
      apiGroup: rbac.authorization.k8s.io
    ```
    

---

# 查看

```bash
# 查看所有 ClusterRoleBinding
kubectl get clusterrolebindings

# 查看 ClusterRoleBinding 详情
kubectl describe clusterrolebinding <binding-name>

# 查看 ClusterRole 权限
kubectl describe clusterrole <clusterrole-name>
```