---
title: Docker Compose 命令
date: 2026-04-07
tags:
  - 基础设施
  - Docker
type: note
status: incomplete
---

## Docker Compose 命令

```bash
## 停止服务并清理所有资源（含数据卷）
docker-compose down -v
```

```bash
## 查看实时日志（指定服务）
docker-compose logs -f --tail=50 service_name
```

```bash
## 构建指定服务并跳过缓存
docker-compose build --no-cache service_name
```

```bash
## 指定自定义环境文件
docker-compose --env-file .env.prod up -d
```