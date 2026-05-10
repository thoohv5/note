---
title: distribution
date: 2026-04-07
tags: [基础设施, Docker, 私有仓库]
type: note
status: complete
source: https://github.com/distribution/distribution
---

## distribution

`distribution` 是 Docker Registry v2 的开源实现，用于存储和分发 OCI/Docker 镜像。常见的私有镜像仓库、镜像代理和企业级 Registry 都建立在相同协议之上。

## 作用

- 接收 `docker push` 上传的镜像层和 Manifest。
- 为 `docker pull` 提供镜像分发能力。
- 支持本地文件系统、对象存储等后端存储。
- 可配合认证、TLS、反向代理和垃圾回收组成私有仓库。

## 最小运行示例

```bash
docker run -d \
  --name registry \
  -p 5000:5000 \
  registry:2
```

推送示例：

```bash
docker tag nginx:latest localhost:5000/nginx:latest
docker push localhost:5000/nginx:latest
```

## 生产注意事项

- 必须启用持久化存储，否则容器删除后镜像数据会丢失。
- 对外访问建议配置 HTTPS，否则客户端需要额外配置 insecure registry。
- 删除镜像后需要执行垃圾回收才能真正释放存储空间。
- 多实例部署要确保共享存储和一致的认证配置。

## 参考

- [distribution/distribution](https://github.com/distribution/distribution)
