# PodPreset（Pod 预设置）

如果您需要在现代 Kubernetes 集群 (v1.21+) 中实现类似 PodPreset 的功能，Mutating Admission Webhook 是最佳选择。以下是完整实现方案：

在这个对象中，凡是他想在开发人员编写的 Pod 里追加的字段，都可以预先定义好。

### 安装

```bash
kubectl create -f https://github.com/kyverno/kyverno/releases/latest/download/install.yaml

# 验证
kubectl api-versions | grep "kyverno.io/v1"
```

### preset.yaml

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: pod-preset-replacement
spec:
  rules:
  - name: inject-config
    match:
      any:
      - resources:
          kinds:
          - Pod
          selector:
            matchLabels:
              role: frontend
    mutate:
      patchStrategicMerge:
        spec:  # 只操作spec，不操作metadata
          containers:
          - (name): "*"
            env:
            - name: DB_HOST
              value:

```

### pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: website
  labels:
    app: website
    role: frontend
spec:
  containers:
    - name: website
      image: nginx
      ports:
        - containerPort: 80
```

### 测试

```bash
kubectl create -f preset.yml
kubectl create -f pod.yml

kubectl get pod website -o yaml
```

结果

```bash
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2025-05-20T13:57:46Z"
  labels:
    app: website
    role: frontend
  name: website
  namespace: default
  resourceVersion: "3950"
  uid: a7c2150d-3e10-41a7-ac28-65a2e4bdff72
spec:
  containers:
  - env:
    - name: DB_HOST
    image: nginx
    imagePullPolicy: Always
    name: website
    ports:
    - containerPort: 80
      protocol: TCP
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-6g8pt
      readOnly: true
```

PodPreset 里定义的内容，只会在 Pod API 对象被创建之前追加在这个对象本身上，而不会影响任何 Pod 的**控制器**的定义。

如果你定义了同时作用于一个 Pod 对象的多个 PodPreset，会发生什么呢？实际上，Kubernetes 项目会帮你合并（Merge）这两个 PodPreset 要做的修改。而如果它们要做的修改有冲突的话，这些冲突字段就不会被修改。