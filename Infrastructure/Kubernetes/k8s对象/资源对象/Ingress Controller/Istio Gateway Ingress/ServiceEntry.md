# ServiceEntry

```yaml
apiVersion: networking.istio.io/v1
kind: ServiceEntry
metadata:
  name: cloud-service-entry
  namespace: default
spec:
  hosts:
    - my-cloud-service.com   # 云服务域名
  location: MESH_EXTERNAL
  ports:
    - number: 80
      name: http
      protocol: HTTP
  resolution: DNS
```

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: ServiceEntry
metadata:
  name: external-mysql
spec:
  hosts:
    - "mysql.local"
  location: MESH_EXTERNAL
  ports:
    - number: 3306
      name: tcp-mysql
      protocol: TCP
  resolution: STATIC
  endpoints:
    - address: 192.168.1.104
      ports:
        tcp-mysql: 3306

```