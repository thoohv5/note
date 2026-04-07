# 用户组（Group）

类型：`kind: Group`

表示用户组，多个用户可以属于同一个组。

- 例如：
    
    ```yaml
    - kind: Group
      name: system:serviceaccounts
    ```
    

Kubernetes 内建了一些特殊 Group，比如：

- `system:authenticated`: 所有通过认证的用户
- `system:unauthenticated`: 未认证用户
- `system:serviceaccounts`: 所有 ServiceAccount 的通用组
- `system:serviceaccounts:<namespace>`: 某个命名空间下的所有 ServiceAccount