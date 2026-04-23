---
title: docker buildx
date: 2026-04-07
  - 基础设施
  - Docker
type: note
status: complete
---

## docker buildx

### 问题

DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
Install the buildx component to build images with BuildKit:
[https://docs.docker.com/go/buildx/](https://docs.docker.com/go/buildx/)

### 解决

```bash
## Linux 安装 buildx
mkdir -p ~/.docker/cli-plugins
curl -SL https://github.com/docker/buildx/releases/download/v0.10.0/buildx-v0.10.0.linux-amd64 -o ~/.docker/cli-plugins/docker-buildx
chmod +x ~/.docker/cli-plugins/docker-buildx

## macOS (Docker Desktop 已内置，无需手动安装)

## 设置为默认构建器
docker buildx install

## 验证
docker buildx ls
```

`docker buildx build -t your-image:tag .`

```bash
## 单平台构建（与旧版 docker build 类似）
docker buildx build -t your-image:tag --load .

## 多平台构建（例如同时构建 amd64/arm64）
docker buildx build --platform linux/amd64,linux/arm64 -t your-image:tag --push .
```