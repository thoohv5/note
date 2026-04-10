---
title: 阿里云Docker Registry
date: 2026-04-07
tags: [基础设施, Docker]
type: note
status: complete
---

# 阿里云Docker Registry

### **Docker将本地镜像发布到阿里云容器镜像服务仓库**

登录阿里云Docker Registry

```yaml
docker login --username=5500*****@qq.com registry.cn-chengdu.aliyuncs.com
```

<aside>
💡

镜像服务的密码和阿里云账户的密码不是同一个密码，需单独配置。

</aside>

重命名镜像

```docker
docker tag IMAGE[:TAG] [REGISTRY_HOST[:REGISTRY_PORT]/]REPOSITORY/IMAGE[:TAG]

docker tag c20987f18b13 registry.cn-chengdu.aliyuncs.com/镜像服务仓库/镜像名称:[镜像版本]
```

将镜像推送到Registry

```docker
docker push [REGISTRY_HOST[:REGISTRY_PORT]/]REPOSITORY/IMAGE[:TAG]

docker push registry.cn-chengdu.aliyuncs.com/镜像服务仓库/镜像名称:[镜像版本号]
```

从Registry拉取镜像

```yaml
docker pull registry.cn-chengdu.aliyuncs.com/镜像服务仓库/镜像名称:[镜像版本号]
```