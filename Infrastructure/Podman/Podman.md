---
title: Podman
date: 2026-04-07
tags:
  - 基础设施
  - Podman
type: note
status: complete
source: https://podman.io/docs
---

## Podman

Podman 是一个 daemonless 的容器管理工具，属于 libpod 生态，可用于创建、运行、管理容器和镜像。它的 CLI 与 Docker 接近，但架构上不依赖常驻守护进程，并且天然支持 rootless 容器。

## 核心特点

- 无中心 daemon，容器进程由用户会话直接管理。
- 支持 rootless 模式，降低宿主机权限风险。
- CLI 与 Docker 命令相近，迁移学习成本较低。
- 支持 Pod 概念，可把多个容器组织到同一网络命名空间中。
- 可配合 Buildah、Skopeo 完成镜像构建和镜像搬运。

## 常用命令

```bash
podman --help
podman search httpd
podman pull docker.io/library/httpd
podman images
podman run -d -p 8080:80/tcp docker.io/library/httpd
podman ps
podman logs -l
podman inspect -l
podman stop -l
podman rm -l
```

官方示例中建议使用完整镜像名，例如 `docker.io/library/httpd`，避免因为 registry 搜索顺序导致拉取到非预期镜像。

## 与 Docker 的区别

- Docker 依赖 Docker daemon，Podman 默认不需要常驻 daemon。
- Podman rootless 是常见使用方式；Docker 虽也支持 rootless，但传统部署更多依赖 root daemon。
- Podman 可以生成 systemd unit，适合在 Linux 主机上托管容器服务。
- Podman 的 Pod 模型更贴近 Kubernetes 中 Pod 的概念。

## 适用场景

- 开发机上无 root 权限运行容器。
- Linux 服务器上以 systemd 管理单机容器服务。
- Docker CLI 兼容场景下的轻量替代方案。
- 学习 Kubernetes Pod 网络和多容器协作模型。

## 注意事项

- rootless 网络和端口映射与 rootful 容器行为不同，排查网络问题时要确认运行模式。
- Mac 和 Windows 通常通过虚拟机运行 Podman，部分 Linux-only 参数不可直接套用。
- 生产环境需要明确镜像仓库、存储驱动、日志和自动重启策略。

## 关联

- [[Docker]]
- [[Buildah]]
- [[Kubernetes]]

[Getting Started with Podman | Podman](https://podman.io/docs)
