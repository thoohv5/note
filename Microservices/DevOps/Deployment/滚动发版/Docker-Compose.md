---
title: Docker-Compose
date: 2026-04-07
tags:
  - 微服务
  - DevOps
type: note
status: complete
---

## Docker-Compose

### docker-compose

```yaml
version: "3.8"

services:
  # Nginx 负责对外暴露 80 端口，并分发流量
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - app

  # Go 业务程序
  app:
    image: your-regsitry/my-endless-app:latest  # 你的镜像名
    deploy:
      replicas: 2              # 保持 2 个副本，提高可用性
      update_config:
        parallelism: 1         # 每次更新 1 个容器
        order: start-first     # 关键：先起新，后停旧
        failure_action: rollback
    stop_grace_period: 30s   # 重点：给 endless 30秒时间处理剩余连接
    healthcheck:
      # 假设你的程序有 /ping 接口返回 200
      test: ["CMD-SHELL", "wget -q --spider http://localhost:8080/ping || exit 1"]
      interval: 5s
      timeout: 3s
      retries: 3
      start_period: 5s
    # 容器内部通信，不直接暴露宿主机端口，避免 start-first 时端口冲突
    expose:
      - "8080"
```

### Nginx 配置

```bash
events { worker_connections 1024; }

http {
    upstream my_service {
        # Docker 会自动将 'app' 解析为所有健康容器的 IP
        server app:8080;
    }

    server {
        listen 80;
        location / {
            proxy_pass http://my_service;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            
            # 保证长连接不会因为 Nginx 默认超时断掉
            proxy_read_timeout 60s;
        }
    }
}
```

### 滚动发布

```bash
docker compose up -d --build
```