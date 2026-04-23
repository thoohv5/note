---
title: Demo
date: 2026-04-07
  - 基础设施
  - K8s
type: note
status: complete
---

## Demo

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grpc-deploy
  labels:
    app: grpc-pod
spec:
  replicas: 3
  selector:
    matchLabels:
      app: grpc-pod
  template:
    metadata:
      labels:
        app: grpc-pod
    spec:
      containers:
      - name: server
        image: "kasunindrasiri/grpc-productinfo-server"
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 50051
          protocol: TCP
        resources:
          requests:
            cpu: "100m"
            memory: "128Mi"
          limits:
            cpu: "500m"
            memory: "512Mi"
        readinessProbe:
          tcpSocket:
            port: 50051
          initialDelaySeconds: 5
        livenessProbe:
          tcpSocket:
            port: 50051
          initialDelaySeconds: 10
          periodSeconds: 15

---
apiVersion: v1
kind: Service
metadata:
  name: grpc-service
spec:
  selector:
    app: grpc-pod
  ports:
  - port: 50051
    targetPort: 50051
    protocol: TCP
  type: ClusterIP
```

```bash
kubectl port-forward service/grpc-service 50051:50051

grpcurl -plaintext localhost:50051 list
grpcurl -plaintext localhost:50051 describe
grpcurl -plaintext localhost:50051 grpc.health.v1.Health/Check

docker run -p 50051:50051 kasunindrasiri/grpc-productinfo-server

export INGRESS_PORT=$(kubectl -n istio-ingress get service istio-ingress -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
```

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: grpc-gateway
  namespace: istio-ingress  # 一定要是你安装 ingress gateway 的命名空间
spec:
  selector:
    app.kubernetes.io/name: istio-ingress
  servers:
  - port:
      number: 80
      name: http2-grpc
      protocol: HTTP2
    hosts:
    - grpc.mesh.com
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: grpc-vs
  namespace: istio-ingress  # 这里要和 Gateway 在同一命名空间
spec:
  gateways:
  - grpc-gateway
  hosts:
  - grpc.mesh.com
  http:
  - match:
    - port: 80
    route:
    - destination:
        host: grpc-service  # grpc-service 服务名，默认当前命名空间
        port:
          number: 50051     # 你的 grpc 服务监听端口

```

```yaml
kubectl -n istio-ingress get pods
istioctl proxy-config listeners istio-ingress-7b44c754cc-hkhv9 -n istio-ingress

kubectl -n istio-ingress get svc istio-ingress -o wide
grpcurl -plaintext -authority grpc.mesh.com  198.19.249.2:80 list

kubectl -n istio-ingress get pod -o wide
grpcurl -plaintext -authority grpc.mesh.com 192.168.66.10:31947 list

```