---
title: 监控 Docker 配置
date: 2026-04-07
  - 微服务
  - 服务治理
type: guide
status: complete
---

## 监控 Docker 配置

### 使用 **cAdvisor** 监控容器：

```yaml
docker run -d --name=cadvisor \
  -p 8080:8080 \
  -v /:/rootfs:ro \
  -v /var/run:/var/run:ro \
  -v /sys:/sys:ro \
  google/cadvisor
```

### 在 **`prometheus.yml`** 中添加：

```yaml
scrape_configs:
  - job_name: "docker"
    static_configs:
      - targets: ["<服务器IP>:8080"]
```

### Grafana模板

- **ID:** `179`
- **名字:** Docker and system monitoring
- **内容:** 容器的 CPU、内存、网络、I/O 监控，适合部署在 Docker 环境。