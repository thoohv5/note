---
title: Harbor
date: 2026-04-07
  - 基础设施
  - Docker
type: note
status: complete
---

## Harbor

Harbor 是 VMware 公司开源的企业级 Docker Registry 项目，其目标是帮助用户迅速搭建一个企业级的 Docker Registry 服务。

Harbor以 Docker 公司开源的 Registry 为基础，提供了图形管理 UI 、基于角色的访问控制(Role Based AccessControl) 、AD/LDAP 集成、以及审计日志(Auditlogging) 等企业用户需求的功能，同时还原生支持中文。

Harbor 的每个组件都是以 Docker 容器的形式构建的，使用 [docker-compose](https://so.csdn.net/so/search?q=docker-compose&spm=1001.2101.3001.7020) 来对它进行部署。用于部署 Harbor 的 docker-compose 模板位于 harbor/docker-compose.yml。

```go
#登陆
docker login -u admin -p Harbor12345 http://127.0.0.1
 
#下载镜像进行测试
docker pull nginx
 
#镜像打标签
docker tag tomcat 127.0.0.1/myproject-lichen/nginx:a1
 
#上传镜像到Harbor
docker push 127.0.0.1/myproject-lichen/nginx:a1
```

## 附录

[Docker--harbor私有仓库部署与管理_harbor仓库-CSDN博客](https://blog.csdn.net/wys_jj/article/details/138421833)